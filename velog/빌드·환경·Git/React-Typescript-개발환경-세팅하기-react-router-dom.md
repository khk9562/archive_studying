---
title: "React + Typescript 개발환경 세팅하기 - react-router-dom"
tags: ["React", "react router dom", "typescript"]
date: 2024-05-01
velog_id: 878e0d66-cebf-448b-a005-5de14393bfaf
velog_url: https://velog.io/@steela/React-Typescript-개발환경-세팅하기-react-router-dom
velog_updated: 2026-07-03T06:33:07.041Z
synced_at: 2026-07-03
---

> 🔗 원본: [velog.io/@steela/React-Typescript-개발환경-세팅하기-react-router-dom](https://velog.io/@steela/React-Typescript-개발환경-세팅하기-react-router-dom) · 📅 2024-05-01
나는 먼저 github에 레포지토리를 만들어서 그걸 클론해오는 방식으로 폴더를 만들어주었다.
그리고 그 폴더를 열어 vscode에서 환경을 세팅해주었다.

아래는 현재 폴더에 리액트와 타입스크립트 환경을 설치해주는 명령어이다.

>  npx create-react-app ./ --template typescript


그리고 타입스크립트 반영해주는 react-router-dom을 설치해주었다.

>  
npm install react-router-dom v6
npm install react-router-dom @types/react-router-dom
npm i --save @types/react @types/react-dom


<br />

index.tsx
```
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);

```

App 컴포넌트를 반드시 BrowerRouter로 감싸주어야 한다.
그리고 AppRouter.ts를 만들어 앞으로 만들 페이지 컴포넌트들을 element로 하여 path를 지정해주려고 했는데.... 폴더 구조를 잘못 짠 건지 Routes 태그에서 자꾸 오류가 났다. 
**'Routes' refers to a value, but is being used as a type here. Did you mean 'typeof Routes'?ts(2749)**

일단 간단한 프로젝트였기 때문에 App 컴포넌트에 바로 라우팅을 해주었다.

```
import React from "react";
import "./App.css";
import Main from "./pages/Main";
import { Routes, Route } from "react-router-dom";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Main />} />
    </Routes>
  );
}

export default App;

```

프로젝트를 진행하면서 라우팅을 따로 분리할 필요가 생기면 추후 업데이트 및 디버깅 하겠다.(24.05.01)
