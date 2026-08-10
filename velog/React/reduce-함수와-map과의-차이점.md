---
title: "reduce 함수와 map과의 차이점"
tags: ["Map", "React", "reduce"]
date: 2024-03-05
velog_id: 93c5dc78-4688-4d7e-839d-c668ddc13310
velog_url: https://velog.io/@steela/reduce-함수와-map과의-차이점
velog_updated: 2026-08-10T08:22:36.407Z
synced_at: 2026-08-10
---

> 🔗 원본: [velog.io/@steela/reduce-함수와-map과의-차이점](https://velog.io/@steela/reduce-함수와-map과의-차이점) · 📅 2024-03-05
## reduce
reduce 함수는 JavaScript 배열의 각 요소를 순회하면서 누적된 결과를 계산하는 함수이다.
```
array.reduce((acc, item) => { 
	// 계산 로직 작성 // 
    }, initialAccumulator);
```

acc: 누적된 결과값을 나타내는 변수. 초기값

item: 배열의 각 요소를 나타내는 변수

initialAccumulator: acc 변수의 초기값


## map
map 함수는 배열의 각 요소에 대해 지정된 로직을 수행하고 새로운 배열을 반환한다.

원본 배열과 동일한 길이의 새로운 배열을 생성하며, 각 요소에 대해 독립적으로 작업을 수행하고자 할 때 사용한다.

 

반면, reduce 함수는 배열의 각 요소를 순회하면서 누적된 결과(단일 값)를 계산한다.

주로 배열의 요소들을 하나로 합치거나, 통계적인 계산을 수행하고자 할 때 사용한다.
