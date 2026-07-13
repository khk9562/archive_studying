---
title: "[React] useEffect와 fetch"
tags: ["Fetch", "React", "useEffect"]
date: 2025-01-19
velog_id: 05ade8f9-7c0a-4724-8467-d10757784910
velog_url: https://velog.io/@steela/React-useEffect와-fetch
velog_updated: 2026-07-13T10:16:36.917Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/React-useEffect와-fetch](https://velog.io/@steela/React-useEffect와-fetch) · 📅 2025-01-19
굉장히 오랜만에 글을 작성한다.
일 관련된 내용을 개인적으로 노션에 정리만 하고, 공개적으로 무언갈 공유하는 일이 굉장히 줄어들어 반성한다..
최근 기초적인 리액트 개념에서 헷갈리는 사항이 생겨 정리 겸 정보 공유 겸 포스팅을 한다.

엄연히 말해 내가 지금껏 사용한 모든 언어는 javascript이지만,
세부적으로 구분하자면 내가 접한 순서는 이렇다.
> jQuery -> javascript -> Next.js -> React

이런 괴랄한(?) 순서로 javascript를 접했고, 실무에 바로 투입되어 일을 하면서 배우다보니 특히 리액트를 사용하는 방법에 문제가 있을 수 있다는 생각을 항상 해왔다. 더군다나 강의를 보고 배운게 아니라 필요한걸 그때 그때 구글링하거나 챗지피티에게 물어보고, 공식 문서를 읽으며 적용하는 방식으로 개발을 해와서 더더욱 그런 의심을 가지고 있었다.

<br/>

**첫 화면 마운트시에 api를 호출하는 방식**을 예로 들어보겠다.

지금까지 나는 보통 아래와 같은 방법을 주로 사용하였다.
```
const getData = async() => {
	try {
    	setIsLoading(true);
        const {status, data} = await axios.get(`/something`);
        if (status === 200) {
        	setData(data);
        }
    } catch (error) {
    	console.error('getData', error);
    } finally {
    	setIsLoading(false);
    }
}

useEffect(() => {
	getData();
},[])
```
만약 getData가 params로 user_id를 필요로한다면,
useEffect가 의존하는 빈배열에 user_id를 넣었다.

<br/>

하지만 최근, 동료의 코드를 확인하고 리액트에서 api를 호출하는 방식에 대해 의문이 생겼다.

```
const getData = useCallback(async() => {
	try {
        const res = await axios.get(`/something`);
        return res.data;
    } catch (error) {
    	console.error(error);
    }
},[])

useEffect(() => {
	getData().then((data) => {setData(data)})
},[getData])
```

<br/>

### 질문
1. useCallback을 어느 때에 사용해야하는가?
2. 단순히 처음 api 호출할 때 useEffect가 의존하는게 빈배열이 아닌 실행되는 자기 자신인 함수인 이유는 무엇인가?
3. 함수 내부에서 상태값에 저장하지 않고 useEffect에서 저장하는 이유는 무엇인가?

<br/>

### 답변
1. 특정 함수의 재생성을 방지해 성능 최적화가 필요할 때 사용한다. 
	- **자식 컴포넌트에 props로 함수**를 내려줄 때, 부모 컴포넌트가 리렌더링될 때마다 자식 컴포넌트도 리렌더링 되는 걸 방지한다.
	- useEffect 등 훅의 의존성 배열에 함수를 넣을 때, **매번 새로 생성되지 않도록 보장**하고 싶을 때.
    
    
2. 이 경우는 이 함수의 변경에 따라 useEffect가 다시 실행되도록 하는 건데, 단순히 초기 마운트시에 함수를 실행하는 거라면 빈배열로 두어도 무방하다. 그리고 함수를 의존성에 넣어도 useCallback으로 제어해두었기 때문에 무한 렌더링은 발생하지 않는다.

3. 함수에서 상태 업데이트를 하지 않는 이유는, 함수를 단순히 api 호출만 할 수 있도록 **책임을 분리**해주어 코드 가독성과 유지 보수성을 높인다. 그리고 api 호출이 완료된 경우에만 상태가 업데이트 되므로 **여러 비동기 호출 간 상태 충돌을 예방**하며 비동기 로직을 관리하는 데도 이점을 갖는다.

<br/>


### 더하여
useEffect에서 단순히 함수를 실행만 하면 비동기 적용이 안된다. 제대로 Promise를 반환하게 하려면 useEffect 안에서 비동기 처리를 해주어야한다.

비동기 작업이 완료된 후에 상태를 업데이트 하려고 하는데, 컴포넌트가 이미 언마운트된 상태라면 에러가 발생할 수 있다.

```
useEffect(() => {
	let isMounted = true; 
    const getData = async() => {
      try {
          const res = await axios.get(`/something`);
          if(isMounted) {
          	setData(res.data);
          }
      } catch (error) {
          console.error(error);
      }
  };

	getData();
    
    return () => {
    	isMounted = false;
	};
},[])
```


### 결론

리액트에서 api 호출을 하는 데에는 여러가지 패턴이 있다는 걸 깨달았고, 특히 메모이제이션 하고 컴포넌트 및 함수를 가장 작은 기능으로 구분하는 것이 좋다는 기본기를 다시금 떠올릴 수 있었다.


<br/>

### 느낀점

지금까지는 기초적인 리액트 훅만을 사용하며 단순 구현에는 문제가 없었기 때문에 하던대로 코딩을 했었다. 그러나 최근 규모가 큰 프로젝트를 맡게 되며 내 코딩방식이 불필요한 렌더링을 초래하는 방식임을 확실히 인지하였다. useMemo, useCallback 등 성능 개선 방법 및 렌더링에 대한 이해도가 부족하다는 걸 뼈저리게 느껴 이번 기회에 정리해본다. 각 패턴별로 장단점이 있으니 어떤 방식의 패턴을 선택하고 지켜나갈지 이걸 동료들과 이야기하는 시간을 가지고 싶다.
