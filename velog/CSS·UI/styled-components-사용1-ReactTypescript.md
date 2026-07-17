---
title: "styled-components 사용1 (React,Typescript)"
tags: ["React", "styled components", "typescript"]
date: 2024-05-01
velog_id: 9433691c-720e-4fcd-a175-f4f52ec8d27e
velog_url: https://velog.io/@steela/styled-components-사용1-ReactTypescript
velog_updated: 2026-07-16T22:13:07.314Z
synced_at: 2026-07-17
---

> 🔗 원본: [velog.io/@steela/styled-components-사용1-ReactTypescript](https://velog.io/@steela/styled-components-사용1-ReactTypescript) · 📅 2024-05-01
기존 업무에서는 기본적인 css만을 사용했었고, 과거에 딱 한 번 Saas를 사용한 이래로 다양한 css 라이브러리를 사용해보지 못했다. 하지만 UI에 관심이 많고 새로운 걸 시도해보는 걸 좋아하며 회사로부터 벗어난 지금 자유로울 때, 간단한 토이 프로젝트를 할 때 styled-components를 프로젝트에 적용해보기로 결정했다 ^0^(매우 신남)

## 1. 라이브러리 설치

> #### "styled-components" 설치
	npm i styled-components
#### "styled-components" 타입 정의 설치
	npm i -D @types/styled-components
#### 제공해주는 "css reset"을 사용한다면 설치
	npm i styled-reset


<br />

## 2. 글로벌 테마 설정
아래 출처에 기입한 블로그를 **매우!** 참조하였다.
shared 폴더를 만들어 전역적인 스타일을 적용한다.

<br />


**@src/shared/global.ts**
global.ts에는 css reset과 전역적으로 커스터마이징할 스타일에 대해 작성한다.
```
import { createGlobalStyle } from "styled-components";
import reset from "styled-reset";

export const GlobalStyle = createGlobalStyle`
  ${reset}

  /* 아래에 추가적으로 적용할 전역 스타일 작성 */
`;

```


<br />
<br />



**@src/shared/theme.ts**
모든 페이지 및 컴포넌트에서 공통으로 사용할 요소를 작성하였다.
styled-components에서 전역적으로 사용할 변수를 지정하는 것이다.


```
import { css } from "styled-components";

/** 자주 사용하는 색상들 */
const colors = {
  bgColor: "#efefef",
  titleColor: "#222",
  pColor: "#333",
  gray: "#343434",
};

/** 반응형 사이즈 */
const mediaSize = {
  xs: "screen and (max-width: '400px')",
  sm: "screen and (max-width: '640px')",
  md: "screen and (max-width: '768px')",
  lg: "screen and (max-width: '1000px')",
};

/** 폰트 크기 */
const fontSize = {
  xs: "0.75rem",
  sm: "0.875rem",
  md: "1rem",
  lg: "1.125rem",
  xl: "1.25rem",
  "2xl": "1.5rem",
};

/** 유틸리티 */
const util = {
  truncate: () => css`
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  `,

  scroll: () => css`
    &::-webkit-scrollbar {
      /** 스크롤바의 너비 */
      width: 4px;
    }
    &::-webkit-scrollbar-thumb {
      /** 스크롤바 길이 */
      height: 25%;
      /** 스크롤바의 색상 */
      background: ${({ theme }) => theme.colors.indigo600};
      border-radius: 10px;
    }
    &::-webkit-scrollbar-track {
      /** 스크롤바 뒷 배경 색상 */
      background: ${({ theme }) => theme.colors.indigo300};
    }
  `,
};

const theme = {
  colors,
  mediaSize,
  fontSize,
  util,
};

export default theme;

/** 타입 재정의를 위함 ( "styled-components" 변수 타입 추론을 위함( 자동완성 ) ) */
export type Theme = typeof theme;

```

<br />

## 3. 스타일 타입 재정의
아래와 같이 타입을 수정했는데, 이것에 관해서는 정확히 왜 사용하는지 추가적인 공부가 필요하다. styled-components 사용시 자동완성을 위해서라고 하는데, 코드를 추가 작성하며 경험한 후 업데이트하겠다.

**@types/styled.ts**
```
import type { CSSProp } from "styled-components";
import type { Theme } from "@src/shared/theme";

declare module "styled-components" {
  export interface DefaultTheme extends Theme {
    /*
     * 필요하다면 여기서 타입을 정의해줘도 됨
     * 하지만 "Theme"가 수정될 때마다 수정사항을 반영해줘야 하기 때문에 "extends Theme"형태로 적는 것이 좋음
     */
  }
}

```

<br />

## 4. 글로벌 스타일 적용
컴포넌트를 라우팅하는 BrowserRouter 태그를 감싸주도록 작성한다.
 
**index.tsx**
  ```
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import "./index.css";

import { ThemeProvider } from "styled-components";
import theme from "./shared/\btheme";
import { GlobalStyle } from "./shared/global";

import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);

root.render(
  <React.StrictMode>
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </ThemeProvider>
  </React.StrictMode>
);

```

  <br />

## 5. 컴포넌트 및 스타일 작성
**@components/SelectButton/index.tsx**
여기에서는 기능 관련 함수와 선택지 버튼 안에 기입될 내용들을 받아올 예정이다.
```
import React, { useState, useEffect } from "react";
import { Link } from "react-router-dom";
import StyledSelectButton from "./style";
import { SelectButtonType } from "../../types/types";
function SelectButton({ children }: SelectButtonType) {
  return <StyledSelectButton>{children}</StyledSelectButton>;
}

export default SelectButton;

```
<br />

**@components/SelectButton/style.tsx**
이곳에서는 위의 SelectButton을 꾸며줄 스타일링 관련해서만 작성할 것이다.
```
import styled, { css } from "styled-components";

const StyledSelectButton = styled.button`
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 3px 6px;
  word-break: keep-all;
  cursor: pointer;
  display: block;
  width: 70%;
  min-height: 80px;
`;

export default StyledSelectButton;

```
**@pages/Select.tsx**
이곳은 위 SelectButton 4개를 사용하여 선택지를 보여주는 등 사용자로부터 이벤트를 입력받을 수 있도록 하는 페이지이다.
```
import React from "react";
import { Link } from "react-router-dom";
import PageNum from "../components/PageNum/PageNum";
import SelectButton from "../components/SelectButton";

function Select() {
  return (
    <article>
      <PageNum />
      <h1>질문</h1>
      <p>
        21세기 자본주의의 나라 대한민국에서 당신은 대학의 철학과에 진학하게
        되었습니다. 과연 철학과에서 어떤 포지션을 맡게 될까요?
      </p>

      <div
        style={{
          display: "flex",
          flexDirection: "column",
          justifyContent: "center",
          gap: "18px",
          margin: "12px",
        }}
      >
        <SelectButton>선택지1</SelectButton>
        <SelectButton>선택지2</SelectButton>
        <SelectButton>선택지3</SelectButton>
        <SelectButton>선택지4</SelectButton>
      </div>
    </article>
  );
}

export default Select;

```
  <br />
오늘은 일단 기초적인 사용법을 익혔다. 이제 관련하여 hover 이벤트나 컴포넌트를 분리하여 어떻게 스타일을 적용하는 게 효율적인 방법일지 고민하는 시간이 필요할 거 같다.





  <br />

> #### 출처
styled-components + TypeScript 세팅 및 사용 예시 https://1-blue.github.io/posts/styled-components/
styled-components 공식 문서 https://styled-components.com/docs/basics
