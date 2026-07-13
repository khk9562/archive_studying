---
title: "상태 관리 라이브러리 선택 고민, Zustand"
tags: ["Context API", "Jotai", "Recoil", "redux-toolkit", "zustand"]
date: 2024-06-16
velog_id: 7cb84eeb-6acf-4a21-96fd-c69b7c24eed4
velog_url: https://velog.io/@steela/상태-관리-라이브러리-선택-고민-Zustand
velog_updated: 2026-07-13T10:16:45.054Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/상태-관리-라이브러리-선택-고민-Zustand](https://velog.io/@steela/상태-관리-라이브러리-선택-고민-Zustand) · 📅 2024-06-16
지금까지의 업무에서는 개발에 막 입문하자마자 결과물을 도출해내는데 바빠서 Context API로 상태 관리 라이브러리를 선정한 이후, 고민할 새 없이 계속해서 그걸 사용하였다.

하지만 이직 전 원티드 프리온보딩 챌린지를 수강하며 Redux-toolkit을 경험해보며 Context API가 사용이 용이하긴 하지만 이것도 그렇게 어렵지 않으니 겁먹지 말고 필요한 사용 목적에 적합하게 라이브러리를 선택해야겠다는 결심을 했다.

그리고 이번에 투입될 프로젝트는 약 4개 정도의 페이지를 가진 소규모이기 때문에 **기존에 사용해보았던 라이브러리(Context API, Redux-toolkit)를 제외**하고, 공부 시간이 필요해 오히려 사용에 효율이 떨어질 거 같은 **Redux을 제외**하며 수많은 상태 관리 라이브러리 중 어느 것을 선택해서 사용할지 고민이 되었다.. 2024년 초 기준, 많이 사용되는 전역 상태관리 라이브러리 중 위 3가지를 제외하니 Recoil, Jotai, Zustand가 남았다.

**결론은 Zustand를 선택했다.**


<br />

## Zustand
![](https://velog.velcdn.com/images/steela/post/6815fbb7-5be7-43e0-b675-dd10e9afaf17/image.png)

Zustand는 간결한 Flux 원칙을 바탕으로, 발행/구독 모델(pub/sub)을 기반으로 이루어져 있다. 스토어의 상태 변경이 일어날 때 실행할 리스너 함수를 모아 두었다가(sub), 상태가 변경됐을 때 등록된 리스너에게 상태가 변경되었다고 알려준다(pub).
그리고 스토어를 생성하는 함수 호출 시 클로저를 사용한다. 즉, 상태를 변경, 조회, 구독하는 인터페이스를 통해서만 상태를 다루고, 실제 상태는 생명 주기에 따라 처음부터 끝까지 의도하지 않는 변경에 대해 막을 수 있다.

### Store
```bash
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
  updateBears: (newBears) => set({ bears: newBears }),
}))
```

### bind components
```bash
function BearCounter() {
  const bears = useStore((state) => state.bears)
  return <h1>{bears} around here...</h1>
}

function Controls() {
  const increasePopulation = useStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>one up</button>
}
```

<br/>

## Zustand와 Recoil, Jotai와의 차이

Recoil과 Jotai는 아토믹 접근을 가지고 만들었다. 아톰은 상태 단위이며, 업데이트와 구독이 가능하다. 아톰이 업데이트 됐을 떄 해당 아톰을 구독하고 있는 컴포넌트들은 리렌더링이 일어나게 된다. 즉 단점으로, 아톰이 여러 군데서 사용될 경우 사이드 이펙트가 발생할 수 있다.

### Jotai와의 차이
1. Zusatnd는 단일 store인 반면, Jotai는 함께 구성될 수 있는 원시 원자로 구성된다.
2. Zustand 저장소는 외부 저장소이므로 React 외부 액세스가 필요할 떄 더 적합하다.
3. Jotai는 atom dependencies(atom object referential identities)를 통해 렌더링 최적화를 달성한다. 그러나 Zustand에서는 Selector를 사용하여 렌더링 최적화를 수동으로 적용한다.


### Recoil과의 차이
1. Recoil은 Jotai와 유사하게 atom의 문자열 키에 따라 달라진다.
2. Recoil은 app을 context provider로 감싸야하고, Zustand는 그렇지 않다.
3. Jotai와 동일한 차이점으로, Zustand는 Selector을 사용하여 렌더링 최적화를 이룬다.

<br />

## 선택 이유
1. 기존 사용해보았던 Redux-toolkit과 유사한 사용 방법이다.
2. 다른 두 라이브러리 보다 사용 빈도가 많은 것으로 추정된다. 다른 개발자분들이 사용하기로 선택한 데는 이유가 있다고 생각한다.
3. 사용 방법이 매우 쉽다.
4. 렌더링에 있어 내가 개입할 수 있는 자유도가 더 높은 점이 좋았다.
5. [Redux Devtools](https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=ko&refresh=1&pli=1)를 통해 상태 디버깅이 가능하다.

<br />

### 참고
[Zustand 공식 문서] https://docs.pmnd.rs/zustand/getting-started/introduction
https://medium.com/@ian-white/recoil-vs-jotai-vs-zustand-09d3c8bd5bc0
https://yozm.wishket.com/magazine/detail/2233/
