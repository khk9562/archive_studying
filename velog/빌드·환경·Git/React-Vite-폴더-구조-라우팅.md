---
title: "[React + Vite] 폴더 구조 라우팅"
tags: ["React", "vite", "라우팅", "폴더구조", "폴더구조 라우팅"]
date: 2024-06-11
velog_id: 71e8dbe7-34df-4552-b59d-1cec195d171f
velog_url: https://velog.io/@steela/React-Vite-폴더-구조-라우팅
velog_updated: 2026-07-14T00:14:28.715Z
synced_at: 2026-07-14
---

> 🔗 원본: [velog.io/@steela/React-Vite-폴더-구조-라우팅](https://velog.io/@steela/React-Vite-폴더-구조-라우팅) · 📅 2024-06-11
Next.js를 사용하다가 React를 쓰려니 라우팅이 너무 귀찮았다. 찾아보던 중 Next.js Pages 구조처럼 라우팅을 가능하게 할 수 있다는 사실을 발견.

Vite는 import.meta.glob 함수를 이용해 여러 모듈을 한 번에 가져올 수 있는 기능을 지원하고 있고, 이를 이용해서 폴더 구조 라우팅을 구현할 수 있다.

> ### import.meta.glob 함수
[참고] https://vitejs.dev/guide/features.html#glob-import


<br />

## react-router-dom 설치
```
npm install react-router-dom
```

<br />

### 폴더 절대 경로 구현

절대경로를 구현해놓으면 import할 때나 @components/blablal.jsx 처럼 간결하게 불러올 수 있다. 없으면 ./src/components/blallfdl.jsx 이런식으로 가독성도, 효율성도 떨어진다.

**[vite.config.js]**
```
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";
import { fileURLToPath } from "url";

// https://vitejs.dev/config/

// __dirname 대체 구현
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
      "@components": path.resolve(__dirname, "./src/components"),
      "@styles": path.resolve(__dirname, "./src/styles"),
      "@assets": path.resolve(__dirname, "./src/assets"),
    },
  },
});

```

<br />

**[jsconfig.json]**
프로젝트 내에서 타입스크립트를 사용하지 않고 있어서 tsconfig.json 대신 이 파일을 생성해주었다.

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@styles/*": ["./src/styles/*"],
      "@utils/*": ["./src/utils/*"]
    }
  }
}

```


<br />



## 구현 설명
### 1. import.meta.glob 함수 사용하여 모듈 가져오기

```
const pages = import.meta.glob("./pages/**/*.jsx", { eager: true });
```
pages/ 폴더에 있는 모든 모듈 로드해온다.

<br />

### 2. 정규표현식 사용하여 모듈의 파일명만 가져오기
```
  const fileName = path.match(/\.\/pages\/(.*)\.jsx$/)?.[1];
  if (!fileName) {
    continue;
  }

  const normalizedPathName = fileName.includes("$")
    ? fileName.replace("$", ":")
    : fileName.replace(/\/index/, "");

```
### 3. routes 배열에 요소 추가
- path - 등록하려는 경로
- Element - 경로에 할당하려는 React 컴포넌트
- loader - 데이터 가져오기 기능 (선택)
- action - form data 제출 기능 (선택)
- ErrorBoundary - path 오류가 났을 때 처리하는 React 컴포넌트 (선택)

```
const routes = [];

for (const path of Object.keys(pages)) {
  const fileName = path.match(/\.\/pages\/(.*)\.jsx$/)?.[1];
  if (!fileName) {
    continue;
  }

  const normalizedPathName = fileName.includes("$")
    ? fileName.replace("$", ":")
    : fileName.replace(/\/index/, "");

  // 페이지 컴포넌트가 제대로 불러와졌는지 확인
  const pageComponent = pages[path].default;
  if (!pageComponent) {
    console.error(
      `Component for path ${path} is not found or not exported correctly.`
    );
    continue;
  }

  routes.push({
    path: fileName === "index" ? "/" : `/${normalizedPathName.toLowerCase()}`,
    Element: pages[path].default,
    loader: pages[path]?.loader,
    action: pages[path]?.action,
    ErrorBoundary: pages[path]?.ErrorBoundary,
  });
}
```

### 4. routes 배열을 RouterProvider에 할당
```
const router = createBrowserRouter(
  routes.map(({ Element, ErrorBoundary, ...rest }) => ({
    ...rest,
    element: <Element />,
    ...(ErrorBoundary && { errorElement: React.createElement(ErrorBoundary) }),
  }))
);
```

## 결과

**[App.jsx]**
```
import React from "react";
import "./App.css";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

const pages = import.meta.glob("./pages/**/*.jsx", { eager: true });
const routes = [];

for (const path of Object.keys(pages)) {
  const fileName = path.match(/\.\/pages\/(.*)\.jsx$/)?.[1];
  if (!fileName) {
    continue;
  }

  const normalizedPathName = fileName.includes("$")
    ? fileName.replace("$", ":")
    : fileName.replace(/\/index/, "");

  // 페이지 컴포넌트가 제대로 불러와졌는지 확인
  const pageComponent = pages[path].default;
  if (!pageComponent) {
    console.error(
      `Component for path ${path} is not found or not exported correctly.`
    );
    continue;
  }

  routes.push({
    path: fileName === "index" ? "/" : `/${normalizedPathName.toLowerCase()}`,
    Element: pages[path].default,
    loader: pages[path]?.loader,
    action: pages[path]?.action,
    ErrorBoundary: pages[path]?.ErrorBoundary,
  });
}

console.log("Generated routes:", routes);

if (routes.length === 0) {
  throw new Error("No routes found. Check your page components and paths.");
}

const router = createBrowserRouter(
  routes.map(({ Element, ErrorBoundary, ...rest }) => ({
    ...rest,
    element: <Element />,
    ...(ErrorBoundary && { errorElement: React.createElement(ErrorBoundary) }),
  }))
);
const App = () => {
  return <RouterProvider router={router} />;
};

export default App;

```

<br />

> ### 참고
https://velog.io/@developer-sora/TIL-React-Vite%EC%97%90%EC%84%9C-next.js%EC%B2%98%EB%9F%BC-%EB%9D%BC%EC%9A%B0%ED%8C%85%ED%95%98%EA%B8%B0feat.typescript
