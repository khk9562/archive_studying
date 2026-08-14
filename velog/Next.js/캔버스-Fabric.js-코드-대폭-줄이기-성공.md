---
title: "캔버스 코드 리팩토링"
tags: ["Next.js 13", "canvas", "fabric.js", "코드리팩토링"]
date: 2024-03-18
velog_id: 1649f0ef-34da-4924-bc94-fb6e2b8d9178
velog_url: https://velog.io/@steela/캔버스-Fabric.js-코드-대폭-줄이기-성공
velog_updated: 2026-08-13T18:31:49.396Z
synced_at: 2026-08-14
---

> 🔗 원본: [velog.io/@steela/캔버스-Fabric.js-코드-대폭-줄이기-성공](https://velog.io/@steela/캔버스-Fabric.js-코드-대폭-줄이기-성공) · 📅 2024-03-18
### Fabric.js를 이용하여 캔버스 페이지를 구현하였고, 리팩토링 중이다.
```
  <FabricJSCanvas className={styles.canvas} onReady={onCanvasReady} />
```

## 리팩토링 전
```
  const onCanvasReady = (canvas: fabric.Canvas) => {
    canvasRef.current = canvas;
	.
    .
    .
    canvas.on("selection:created", () => {
      const activeObject = canvas.getActiveObject();
      if (activeObject) {
        if (activeObject.type === "image") {
			생략
        } else {
			생략
        }

        if (activeObject.type === "group") {
			생략
        } else if (activeObject.type == "activeSelection") {
         	생략
        } else if (
          activeObject.type === "rect" ||
          activeObject.type === "circler" ||
          activeObject.type == "triangle"
        ) {
			생략
        } else if (activeObject.type == "text") {
			생략
        } else {

        }
      }
      }
    });

    canvas.on("selection:updated", (e) => {
      const activeObject = canvas.getActiveObject();
      if (activeObject) {
        if (activeObject.type === "image") {
			생략
        } else {
			생략
        }

        if (activeObject.type === "group") {
			생략
        } else if (activeObject.type == "activeSelection") {
         	생략
        } else if (
          activeObject.type === "rect" ||
          activeObject.type === "circler" ||
          activeObject.type == "triangle"
        ) {
			생략
        } else if (activeObject.type == "text") {
			생략
        } else {

        }
      }
    });
  };

```

위와 같은 코드를 무슨 생각으로 작성했을까?
생각을 안했으니 저런 코드가 나왔겠지..?

먼저 activeObject의 타입별로 무수히 많은 다른 기능을 수행하고 있으며,
이는 처음 선택한 객체를 인지하는 selection:created와 그 다음 다른 객체를 선택하면 인지하는 selection:updated 안의 코드가 완전히 일치한다.

useRef와 canvas와 fabric.js에 대한 이해도가 현저히 낮은 탓에
cavasRef와 activeObject가 onCanvasReady 함수 바깥으로 탈출할 수 없다는 이상한 굳은 믿음을 가지고 저런 코드를 작성한 것으로 보인다..


먼저 activeObject의 type을 state로 관리해주고,
useEFfect로 그에 의존하여 위의 if 지옥 함수들을 switch 구문으로 변경하여 작성해주었다.


## 리팩토링 후

```
  useEffect(() => {
    console.log("activeType", activeType);

    const canvas = canvasRef.current;
    if (canvas) {
      const activeObject = canvas.getActiveObject();
      if (activeObject) {
        console.log("activeObject", activeObject);
        switch (activeType) {
          case "image":
			생략
            break;
          case "group":
			생략
            break;
          case "activeSelection":
            생략
            break;
          case "rect":
          case "triangle":
          case "circle":
			생략
            break;
          case "text":
            생략
            break;
          default:
			생략
            break;
        }
      }
    }
  }, [activeType]);
```

와 코드 100줄이 줄었다...!
다만 객체를 선택하지 않았을 때 몇몇 요소들을 사라지게 설정해뒀는데,
state가 빈값이 아닌채로 남아있어 이것만 추가로 처리해줬어야 했다.

그리고 Fabric.js에서는 친절하게도 selection:cleared라는 기능을 지원한다!
onCanvasReady 함수 안에 아래 코드만 추가해주면 기존과 동일하게 작동하는 코드 완성 !!
```
    canvas.on("selection:cleared", (e) => {
      setActiveType("");
    });
```

**
코드를 작성하면서 생각하지말고,
생각하고 코드를 작성하자 !**
