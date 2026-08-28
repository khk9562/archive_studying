---
title: "로컬 스토리지 저장 VS props 및 상수 저장"
tags: ["Props", "React", "const", "localstorage", "troubleshootings"]
date: 2024-05-08
velog_id: 9fb82295-aaed-4c87-bbcb-56b2f68abe3e
velog_url: https://velog.io/@steela/로컬-스토리지-저장-VS-props-및-상수-저장
velog_updated: 2026-08-28T04:14:02.890Z
synced_at: 2026-08-28
---

> 🔗 원본: [velog.io/@steela/로컬-스토리지-저장-VS-props-및-상수-저장](https://velog.io/@steela/로컬-스토리지-저장-VS-props-및-상수-저장) · 📅 2024-05-08
## 문제 상황
mbti test 같은 간단한 토이 프로젝트를 직접 기획 및 코딩하고 있었다.
전역관리 라이브러리 사용 없이 페이지마다 선택한 결과값을 로컬스토리지에 저장하고, 페이지가 변경될 때마다 로컬스토리지에서 결과를 받아와 선택된 거에만 active 클래스를 적용해 색을 다르게 보여주려고 했다.

하지만 이 경우, **마지막 페이지에서 선택한게 바로 스타일링 되지 않아 무엇을 선택했는지 사용자로 하여금 알 수 없게 하는 치명적인 오류**가 나타났다. 이전 페이지들은 선택 시 바로 다음 페이지로 넘어가게끔 세팅해서 이 사실을 미리 고려하지 않았다.. 처음부터 설계를 잘못한 것 ㅠㅠ

![](https://velog.velcdn.com/images/steela/post/2d4a0f35-711d-40f9-b8c8-ca3f3a609ab2/image.png)

**[@pages/Select.tsx]**
```
function Select() {
  const [pageNum, setPageNum] = useState<number>(1);
  const [quest, setQuest] = useState<string>("");
  const [selects, setSelects] = useState<any>();

  const onClick = (page: number, result: string) => {
    localStorage.setItem(`page${page}`, result);
  };

  function findMostFrequentValue() {
    // 값을 저장할 객체 초기화
    const valueCounts: Record<string, number> = {};

    // localStorage에서 값을 읽어와 빈도수 계산
    for (let i = 1; i <= SELECT_LIST.length; i++) {
      const value = localStorage.getItem(`page${i}`);
      if (value) {
        if (valueCounts[value]) {
          valueCounts[value]++;
        } else {
          valueCounts[value] = 1;
        }
      }
    }

    // 가장 많이 나온 값을 찾기
    let mostFrequentValue = "";
    let maxCount = 0;
    for (const value in valueCounts) {
      if (valueCounts[value] > maxCount) {
        mostFrequentValue = value;
        maxCount = valueCounts[value];
      }
    }

    if (mostFrequentValue) {
      localStorage.setItem("result", mostFrequentValue);
    } else {
      alert("결과 확인 중 에러 발생");
    }
  }

  useEffect(() => {
    let page = localStorage.getItem("page");
    setPageNum(Number(page));
  }, []);

  useEffect(() => {
    const currentPageData = SELECT_LIST.find((item) => item.page == pageNum);

    if (currentPageData) {
      setQuest(currentPageData.title);
      setSelects(currentPageData.contents);
    }
  }, [pageNum]);

  return (
    <Container>
      <PageNum pageNum={pageNum} setPageNum={setPageNum} />
      <Question>{quest}</Question>

      <div
        style={{
          width: "95%",
          display: "flex",
          flexDirection: "column",
          justifyContent: "center",
          gap: "8px",
          margin: "12px",
        }}
      >
        {selects?.map((item: any, index: number) => (
          <SelectButton
            key={`selectButton${item.page}-${item.result}`}
            onClick={() => {
              onClick(pageNum, item.result);

              if (pageNum < SELECT_LIST.length) {
                setPageNum(pageNum + 1);
              }
            }}
          >
            {item.text}
          </SelectButton>
        ))}
```

**[@components/SelectButton/index.tsx] **
```
function SelectButton({ children, onClick }: SelectButtonType) {
  const [isActive, setIsActive] = useState<boolean>(false);
  // focus 이벤트 핸들러
  const handleFocus = () => {
    setIsActive(true);
  };

  // blur 이벤트 핸들러
  const handleBlur = () => {
    setIsActive(false);
  };

  // 조건부 클래스 이름을 설정합니다.
  const buttonClassName = isActive ? "active" : "";

  return (
    <StyledSelectButton
      className={buttonClassName}
      onFocus={handleFocus}
      onBlur={handleBlur}
      onClick={onClick}
    >
      {children}
    </StyledSelectButton>
  );
}

```
<br />
<br />


## 고민
#### 1. 클릭할 때 클래스 적용?
그래서 클릭 시에 로컬 스토리지 결과와 클릭한 선택지가 같으면 스타일이 적용되게끔 하려고 생각했다가, 클릭할 때마다 로컬 스토리지 결과를 가져와야 하고 나머지 선택지들의 클래스를 제거해줘야 하는 귀찮음이 더 생겼다.

#### 2. 전역 라이브러리 사용?

정말 전역 라이브러리를 사용해야만 하는 건가? 라는 고민에 빠졌는데…
사실 이렇게 간단한 건 Context api를 사용하면 되지만, Context api는 많이 사용해봐서 이왕 전역 상태관리 라이브러리를 사용할 거면 Redux toolkit을 적용하고 싶은 욕심이 있었다. 하지만 이 프로젝트는 그걸 사용하기엔 너무 간단한 프로젝트라서 빨리 끝내서 다음 프로젝트에 적용하고 싶었다........

<br />


## 해결

현재 페이지 넘버와 최종 결과만 localStorage에 저장하고,
페이지마다 선택 결과는 컴포넌트를 포함하는 페이지에서 **배열**로 결과값을 저장하는 형태로 변경했다.
최종 결과를 도출하기 위해 결과 계산하는 과정과 SelectButton type 설정도 같이 수정해주었다.

![](https://velog.velcdn.com/images/steela/post/55b61918-75b8-44bb-aac8-0581fa1400e7/image.png)


**[@pages/Select.tsx]**

```
  const onClick = (page: number, result: string) => {
    if (pageNum < SELECT_LIST.length) {
      setPageNum(pageNum + 1);
    }
    setSelections((prevSelections) => {
      // 동일한 페이지에 대한 기존 선택을 필터링하여 제거합니다.
      const filteredSelections = prevSelections.filter(
        (selection) => selection.page !== page
      );
      // 필터링된 배열에 새로운 선택을 추가합니다.
      return [...filteredSelections, { page, result }];
    });
  };

  function findMostFrequentValue(selections: any[]) {
    if (selections.length < SELECT_LIST.length) {
      alert("모든 선택지를 선택해주세요.");
      return;
    }
    // 값을 저장할 객체 초기화
    const valueCounts: Record<string, number> = {};

    selections.forEach((selection) => {
      const value = selection.result;
      if (value) {
        if (valueCounts[value]) {
          valueCounts[value]++;
        } else {
          valueCounts[value] = 1;
        }
      }
    });

    // 가장 많이 나온 값을 찾기
    let mostFrequentValue = "";
    let maxCount = 0;
    for (const value in valueCounts) {
      if (valueCounts[value] > maxCount) {
        mostFrequentValue = value;
        maxCount = valueCounts[value];
      }
    }

    if (mostFrequentValue) {
      localStorage.setItem("result", mostFrequentValue);
    } else {
      alert("결과 확인 중 에러 발생");
    }
  }

  useEffect(() => {
    let page = localStorage.getItem("page");
    setPageNum(Number(page));
  }, []);

  useEffect(() => {
    const currentPageData = SELECT_LIST.find((item) => item.page == pageNum);

    if (currentPageData) {
      setQuest(currentPageData.title);
      setSelects(currentPageData.contents);
    }
  }, [pageNum]);
```

<br />

**[@components/SelectButton/index.tsx] **

```
  const [isActive, setIsActive] = useState<boolean>(false);

  useEffect(() => {
    const selectedResult = selections.find(
      (selection) => selection.page === pageNum
    );

    if (selectedResult && selectedResult.result === result) {
      setIsActive(true);
    } else {
      setIsActive(false);
    }
  }, [selections, pageNum, result]);
```


나는 바보다. 하지만 개발은 재미있다. 계속 더 더 더 해서 바보같은 시도를 줄여야지 !
