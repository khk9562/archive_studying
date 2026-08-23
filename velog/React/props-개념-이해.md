---
title: "props 개념 이해"
tags: ["Props", "React"]
date: 2024-03-05
velog_id: 2f5d8198-15c8-4cc5-8be2-a4dda202be40
velog_url: https://velog.io/@steela/props-개념-이해
velog_updated: 2026-08-22T23:32:37.105Z
synced_at: 2026-08-23
---

> 🔗 원본: [velog.io/@steela/props-개념-이해](https://velog.io/@steela/props-개념-이해) · 📅 2024-03-05
특정 페이지 또는 컴포넌트에서 컴포넌트로 변수 값을 전달할 수 있다.
예를 들어, App.jsx에서 Hello.jsx로 name이라는 변수 값을 전달해보겠다.


### App.jsx
```
import React from "react";
import Hello from "./components/Hello";

function App() {
  return <Hello name="react"></Hello>;
}

export default App;
```

### components/Hello.jsx
```
import React from "react";

function Hello(props) {
  return <div>Hello {props.name}</div>;
}
export default Hello;
```



페이지 또는 컴포넌트 내부에서 코드를 작성하고,
```
<Hello name="이름" />
```
받는 쪽은 props라는 예약어를 통해 받음
  
  ```
function Hello(props) {
	return <div>{props.name}</div>
}
```


 

App, 즉 메인 페이지에서는 Hello react라는 글자가 나타날 것이다.
