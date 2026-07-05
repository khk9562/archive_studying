---
title: "리액트 로컬 폰트 설정 (Global Style with styled-components )"
tags: ["React", "fonts", "global style", "styled component"]
date: 2024-05-02
velog_id: d20cf5d4-7bac-4b6f-9812-1f6177c73af8
velog_url: https://velog.io/@steela/리액트-로컬-폰트-설정-Global-Style-with-styled-components
velog_updated: 2026-07-04T20:56:34.330Z
synced_at: 2026-07-05
---

> 🔗 원본: [velog.io/@steela/리액트-로컬-폰트-설정-Global-Style-with-styled-components](https://velog.io/@steela/리액트-로컬-폰트-설정-Global-Style-with-styled-components) · 📅 2024-05-02
먼저 assets 폴더 안에 fonts 폴더를 만들어주고, 거기에 Pretendard 폰트를 woff 확장자로 넣어주었다. (@assets/fonts)
그리고 폰트를 불러와서 기본 폰트로 세팅하기 위해 Global Style로 설정하였다. 이것은 전의 styled-components 기본 환경 세팅하는 페이지를 참조하면 더욱 이해하기 쉬울 것이다.
https://velog.io/@steela/styled-components-%EC%82%AC%EC%9A%A91-ReactTypescript

<br />

**@shared/global.ts**
```
import { createGlobalStyle } from "styled-components";
import reset from "styled-reset";

export const GlobalStyle = createGlobalStyle`
  ${reset}

  /* 아래에 추가적으로 적용할 전역 스타일 작성 */
  @font-face {
    font-family: 'Pretendard';
    font-weight: 300;
    src: url('./assets/fonts/Pretendard-Light.woff') format('woff'),
  }
  @font-face {
    font-family: 'Pretendard';
    font-weight: 400;
    src: url('./assets/fonts/Pretendard-Light.woff') format('woff'),
  }
  @font-face {
    font-family: 'Pretendard';
    font-weight: 500;
    src: url('./assets/fonts/Pretendard-Medium.woff') format('woff'),
  }
  @font-face {
    font-family: 'Pretendard';
    font-weight: 600;
    src: url('./assets/fonts/Pretendard-Bold.woff') format('woff'),
  }
  @font-face {
    font-family: 'Pretendard';
    font-weight: 800;
    src: url('./assets/fonts/Pretendard-ExtraBold.woff') format('woff'),
  }
  @font-face {
    font-family: 'Pretendard';
    font-weight: 900;
    src: url('./assets/fonts/Pretendard-Black.woff') format('woff'),
  }
  
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  html, body, #root {
    font-family: "Pretendard";
    font-weight: 400;
    letter-spacing: -0.02px;
  }

`;

```

font-face로 굵기별로 폰트를 불러오고, html, body, #root에 폰트 종류와 굵기 및 자간을 미리 설정해주었다.

<br />

**@index.tsx**
```
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import "./index.css";

import { ThemeProvider } from "styled-components";
import theme from "./shared/theme";
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

그리고 반드시 App 컴포넌트 바깥에 GlobalStyle을 미리 사용해주어 스타일이 미리 적용되도록 하면 간단하게 완료!

