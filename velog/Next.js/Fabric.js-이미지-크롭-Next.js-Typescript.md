---
title: "[Next.js + Typescript] 이미지 크롭 with Fabric.js"
tags: ["Next.js 13", "canvas", "crop", "fabric.js", "typescript"]
date: 2024-03-24
velog_id: c389429e-49ce-4abe-9a3c-2d4fa71abfd6
velog_url: https://velog.io/@steela/Fabric.js-이미지-크롭-Next.js-Typescript
velog_updated: 2026-07-17T06:19:43.804Z
synced_at: 2026-07-17
---

> 🔗 원본: [velog.io/@steela/Fabric.js-이미지-크롭-Next.js-Typescript](https://velog.io/@steela/Fabric.js-이미지-크롭-Next.js-Typescript) · 📅 2024-03-24
### 업로드 한 이미지 캔버스에 드래그앤드롭으로 추가
먼저 node.js 기반 로컬 서버에 이미지를 업로드하는 api를 사용하여 이미지를 불러왔다. 해당 내용은 아래 링크를 참고해주길 바란다.

[Next.js 로컬 서버 이미지 업로드](https://velog.io/@steela/Next.js-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%97%85%EB%A1%9C%EB%93%9C-api)


그리고 그 이미지를 아래의 코드로 **캔버스 위로 드래그 앤 드롭하여 캔버스에 해당 이미지를 추가**하였다.

```
  const addImage = (source: string) => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    fabric.Image.fromURL(
      source,
      function (img) {
        if (!img) {
          console.error(`Could not create image from ${source}`);
          return;
        }

        img
          .scale(0.2)
          .set({
            left: 0,
            top: 0,
            hasControls: true,
          })
          .setCoords();

        canvas.add(img);
        canvas.requestRenderAll();
      },
      { crossOrigin: "anonymous" }
    );
  };
```

```

<div
	className={styles.imgs}
    key={`image-${index}`}
    draggable="true"
    onDragStart={(e) => {
		e.dataTransfer.setData("text/plain", image);
	}}
>
	<Image
		src={image}
		alt={`Image ${index}`}
		width={180}
		height={180}
	/>
</div>

.
.
.

 <div
	className={styles.canvasBox}
    style={canvasPosition}
    onDrop={(e) => {
		e.preventDefault();
		const imageUrl = e.dataTransfer.getData("text/plain");
		if (imageUrl) {
			addImage(imageUrl);
		}
	onDragOver={(e) => e.preventDefault()}
>
	<FabricJSCanvas className={styles.canvas} onReady={onCanvasReady} />
</div>
```


## 크롭 기능 구상

#### 조건1
선택한 객체의 타입이 image인 경우에만 크롭 버튼이 나타나게 활성화
#### 조건2
크롭 버튼을 누른 경우에 (크롭 모드를 활성화시켜) 모든 객체의 이동을 제어하기 위해 모든 객체 선택을 불가능하게 함.
#### 조건 3
선택했던 이미지 위에서 드래그 앤 드롭했을 때 사각형이 나타나면서 사용자에게 크롭이 될 부분의 가이드라인 보여주기
#### 조건 4
기존 이미지는 삭제하고, 크롭 가이드라인과 동일한 영역의 이미지를 캔버스에 추가


## 크롭 기능 구현
먼저, 크롭 버튼 활성화의 기준이 될 state와 크롭 버튼이 눌렸는지 확인할 state, 그리고 선택한 이미지 타입의 객체를 담을 state와 크롭 가이드라인, 즉 크롭 영역을 담을 state가 필요하다.
```
  const [showCropBtn, setShowCropBtn] = useState<boolean>(false);
  const [isCropMode, setIsCropMode] = useState<boolean>(false);
  const [cropZone, setCropZone] = useState<fabric.Rect | null>(null);
  const [selectedImage, setSelectedImage] = useState<fabric.Image | null>(null);
```

이미지 선택시 드러날 버튼의 jsx 코드는 생략하겠다.

### 크롭 모드 활성화
다음으로, 크롭 버튼이 드러났을 때 
1) 그 버튼을 누르면 isCropMode를 true 처리하고
2) 객체 선택을 해제한 후
(크롭 가이드라인 영역 드래그할 때 객체가 선택되어 같이 따라오면 안되므로),
3) 선택한 객체를 state에 담아주고
4) 맨 앞으로 보내주는 함수를 작성하였다.
이 4가지를 완료한 상태를 크롭 모드 활성화라고 부르겠다.
그리고 그 함수가 handleCropMode이다.

```
  const handleCropMode = () => {
    if (showCropBtn) {
      setIsCropMode(true);
      const c = canvasRef.current;
      if (c) {
        const ac = c.getActiveObject();
        if (ac && ac.type === "image") {
          ac.selectable = false;
          setSelectedImage(ac as fabric.Image);
          c.bringToFront(ac);
        }
        c.discardActiveObject().renderAll();
      }
    } else {
      setSelectedImage(null);
    }
  };
```

또한 isCropMode가 true일 때, 즉 크롭 실행 중일 때 모든 객체를 선택 불가능하게 만들어야 한다. 이것은 위의 2번의 괄호와 같은 이유 때문에 추가되었다.

```
  useEffect(() => {
    const canvas = canvasRef.current;

    if (canvas) {
      canvas.getObjects().forEach((obj) => {
        if (isCropMode) {
          obj.selectable = false; // 크롭 모드일 때 모든 객체 선택 불가
        } else {
          obj.selectable = true; // 크롭 모드가 아닐 때 모든 객체 선택 가능
        }
      });

      canvas.renderAll(); // 캔버스 갱신
    }
  }, [isCropMode]);
```

### 크롭 영역 구현
다음으로는 크롭 가이드라인, 즉 크롭존을 그리는 방법이다.
이는 크게 3단계로 나눌 수 있는데,
1) 마우스 좌측 버튼을 눌렀을 때
2) 마우스 드래그를 할 때
3) 마우스 좌측 버튼 누르는 것을 해제했을 때

```
  useEffect(() => {
    const canvas = canvasRef.current;

    function handleMouseDown(options: fabric.IEvent) {
      if (!isCropMode || !canvas) return;

      const pointer = canvas.getPointer(options.e);

      const rect = new fabric.Rect({
        left: pointer.x,
        top: pointer.y,
        width: 1,
        height: 1,
        fill: "transparent",
        stroke: `black`,
        hasRotatingPoint: false,
      });

      setCropZone(rect);
      canvas.add(rect);
      canvas.setActiveObject(rect);
    }

    function handleMouseMove(options: fabric.IEvent) {
      if (!isCropMode || !canvasRef.current || !cropZone) return;

      const pointer = canvasRef.current.getPointer(options.e);

      let posX = Math.min(pointer.x, cropZone.left ?? pointer.x);
      let posY = Math.min(pointer.y, cropZone.top ?? pointer.y);

      let width = Math.abs(pointer.x - (cropZone.left ?? pointer.x));
      let height = Math.abs(pointer.y - (cropZone.top ?? pointer.y));

      cropZone.set({
        left: posX,
        top: posY,
        width: width,
        height: height,
      });

      canvasRef.current.renderAll();
    }

    function handleMouseUp() {
      if (cropZone) {
        handleApplyCrop(selectedImage);
        setIsCropMode(false);
        setCropZone(null);
        if (canvasRef.current) {
          canvasRef.current.discardActiveObject().renderAll();
        }
      }

      if (selectedImage) {
        selectedImage.selectable = true;
      }
    }

    if (canvas) {
      canvas.on("mouse:down", handleMouseDown);
      canvas.on("mouse:move", handleMouseMove);
      canvas.on("mouse:up", handleMouseUp);

      return () => {
        canvas.off("mouse:down", handleMouseDown);
        canvas.off("mouse:move", handleMouseMove);
        canvas.off("mouse:up", handleMouseUp);
      };
    }
  }, [isCropMode, selectedImage, cropZone]);

```

### 크롭 영역만큼의 이미지 드러내기
다음으로는, 선택한 이미지를 크롭 가이드라인 영역만큼 복제해서 드러내는 함수, 즉 실제로 크롭이 시행되는 함수이다.

```
  const handleApplyCrop = async (selectedImage: any) => {
    if (cropZone && selectedImage) {
      const left = cropZone.left ?? 0;
      const top = cropZone.top ?? 0;
      const width = cropZone.width ?? 0;
      const height = cropZone.height ?? 0;

      var croppedCanvas = document.createElement("canvas");

      croppedCanvas.width = width / (selectedImage.scaleX || 0);
      croppedCanvas.height = height / (selectedImage.scaleY || 0);

      var context = croppedCanvas.getContext("2d");
      if (!context) {
        console.error("크롭 에러");
        setShowCropErr(true);
      }
      if (context) {
        context.drawImage(
          (selectedImage as any)._element as HTMLImageElement,
          (left - (selectedImage.left || 0)) / (selectedImage.scaleX || 1),
          (top - (selectedImage.top || 0)) / (selectedImage.scaleY || 1),
          width / (selectedImage.scaleX || 1),
          height / (selectedImage.scaleY || 1),
          0,
          0,
          width / (selectedImage.scaleX || 1),
          height / (selectedImage.scaleY || 1)
        );
      }
      ``;

      var croppedImageURL = croppedCanvas.toDataURL();

      fabric.Image.fromURL(croppedImageURL, function (croppedImg) {
        if (canvasRef.current) {
          croppedImg.set({
            left: left,
            top: top,
            scaleX: selectedImage.scaleX,
            scaleY: selectedImage.scaleY,
          });

          canvasRef.current.remove(selectedImage);
          canvasRef.current.remove(cropZone);
          canvasRef.current.add(croppedImg);

          canvasRef.current.renderAll();
          // setIsCropMode(false);
          setCropZone(null);
        }
      });
    }
  };
```

**우선적으로 이미지 크롭 기능 자체를 구현하는 것에만 집중하였다. 추후 코드를 리팩토링 한다면 추가 업데이트 하겠다.(2024.03.25)**
