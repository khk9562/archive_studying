---
title: "[React] 구글 애널리틱스 연동-2"
tags: ["GA", "Google Analytics", "React"]
date: 2024-05-11
velog_id: 927d6c97-a6f6-40c4-bd8f-63d4c2e032fa
velog_url: https://velog.io/@steela/React-구글-애널리틱스-도입-2
velog_updated: 2026-07-15T21:55:56.714Z
synced_at: 2026-07-16
---

> 🔗 원본: [velog.io/@steela/React-구글-애널리틱스-도입-2](https://velog.io/@steela/React-구글-애널리틱스-도입-2) · 📅 2024-05-11
## react-ga4 라이브러리 설치 및 사용
> 
npm i react-ga4

npm으로 react-ga4 라이브러리를 설치하고,
.env에 측정 ID를 넣어준다.
그리고 app.tsx에서 import 후 처음 페이지 렌더링 시 초기화 해준다.

```
import ReactGA from "react-ga4";

function App() {
  useEffect(() => {
    ReactGA.initialize(`${process.env.REACT_PUBLIC_GA_ID}`);
  }, []);
  .
  .
  .
  
```


## url 주소 바뀔 때마다 GA 감지
React.js의 경우 SPA이기때문에, 프로젝트의 최상단(App.tsx)에 위치해야한다고 한다.
pathname이 변경될때마다 감지되게 하려고 하므로 useLocation 함수를 사용하여 pathname에 의존하여 아래 코드가 실행되게 한다.

**App.tsx**

```
import { useLocation } from "react-router-dom";

function App() {
  const location = useLocation();
  
    useEffect(() => {
    ReactGA.send({
      hitType: "pageview",
    });
  }, [location]);
  
  .
  .
  .

```

_**vercel에서 한국 도메인 이슈로 사이트가 작동하지 않고 있다.(24년 5월 11일 오후 3시 40분 기준) 복구되는 대로 감지되는 지 확인해서 수정하겠다...!**_
