---
title: "[React] styled-component 선택자 중첩 스타일링 방법"
tags: ["React", "styled component", "typescript"]
date: 2024-05-01
velog_id: aeec5013-70b8-44b6-a2d1-e614edf60f8d
velog_url: https://velog.io/@steela/styled-component-선택자-중첩-스타일링-방법
velog_updated: 2026-07-29T02:26:31.533Z
synced_at: 2026-07-29
---

> 🔗 원본: [velog.io/@steela/styled-component-선택자-중첩-스타일링-방법](https://velog.io/@steela/styled-component-선택자-중첩-스타일링-방법) · 📅 2024-05-01
현재 PageNum 폴더 안의 index.tsx에서 기능적인 부분을 구현하는데, styled-component 라이브러리를 사용하여 nav 와 그 안의 button을 한 번에 스타일링 하고 싶었다.

**@components/PageNum/index.tsx**
```
import React, { useState, useEffect } from "react";
import { Link } from "react-router-dom";
import StyledPageNumBox from "./style";

function PageNum() {
  const [pageNum, setPageNum] = useState<number>(1);

  const handlePageNext = () => {
    setPageNum(pageNum + 1);
  };
  const handlePagePrev = () => {
    if (pageNum > 1) {
      setPageNum(pageNum - 1);
    }
  };

  useEffect(() => {
    localStorage.setItem("page", String(pageNum));
  }, [pageNum]);

  return (
    <StyledPageNumBox>
      <button type="button">
        <Link to={"/"}>메인으로</Link>
      </button>
      <button type="button" onClick={handlePagePrev}>
        이전
      </button>
      <div>
        <span>{pageNum}</span>
        <span>/</span>
        <span>전체페이지 수</span>
      </div>
      <button type="button" onClick={handlePageNext}>
        다음
      </button>
    </StyledPageNumBox>
  );
}

export default PageNum;

```

**@components/PageNum/style.tsx**
```
import styled, { css } from "styled-components";

const StyledPageNumBox = styled.nav`
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;

    button {
      background-color: #f0f0f0; // 버튼 배경색
      color: #333; // 버튼 글자색
      border: none; // 테두리 제거
      padding: 8px 16px; // 안쪽 여백
      cursor: pointer; // 마우스 커서 모양 변경
  
      &:hover {
        background-color: #e0e0e0; // 마우스 오버 시 배경색 변경
      }
`;

export default StyledPageNumBox;

```

styled.nav를 지정해주고, 그 안에 SASS처럼 바로 선택자와 중괄호를 입력하고 스타일만 추가해주면 가능하다! 쉽다!
