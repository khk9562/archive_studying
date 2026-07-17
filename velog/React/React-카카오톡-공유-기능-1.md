---
title: "[React] 카카오톡 공유 기능 - 1"
tags: ["React", "카카오톡 공유"]
date: 2024-05-09
velog_id: 5d6bb89a-b1bb-4ae8-8241-d8ac0575d54c
velog_url: https://velog.io/@steela/React-카카오톡-공유-기능-1
velog_updated: 2026-07-16T20:43:49.428Z
synced_at: 2026-07-17
---

> 🔗 원본: [velog.io/@steela/React-카카오톡-공유-기능-1](https://velog.io/@steela/React-카카오톡-공유-기능-1) · 📅 2024-05-09
mbti 선택 같은 토이 프로젝트를 만드는 중,
편하게 공유하는 방법을 적용하고 싶어 카카오톡 공유 기능을 추가하려 한다.

#### 1. 카카오톡 개발자 사이트 접속 및 로그인 후 애플리케이션 생성
먼저 카카오톡 개발자 사이트(https://developers.kakao.com/)에 로그인 후, 내 어플리케이션에 어플리케이션을 추가한다.

#### 2. 사업자등록번호가 없어서 개인 개발자 비즈앱 전환
원하는 기능을 추가하려면 사업자등록번호가 필요하다고 하는데, 나는 사업자가 아니어서 일단은 이 사이트에서 시키는 대로 해보았다.
(참고: https://developers.kakao.com/docs/latest/ko/getting-started/app)
사업자등록번호가 없는 개발자의 경우, 비즈앱으로 전환할 수 있으며 추후 사업자 정보를 등록할 수 있게 된다.
![](https://velog.velcdn.com/images/steela/post/01a51776-28cd-4d1a-b51f-c7d7f155ece0/image.png)

![](https://velog.velcdn.com/images/steela/post/f49e8f6a-b685-4407-abd0-3961cd201f47/image.png)

#### 3. 플랫폼 - Web에 도메인 등록
나는 배포 웹과 로컬호스트를 같이 등록해주었다. 엔터쳐서 줄바꿈하구 그냥 입력하면 된다.
![](https://velog.velcdn.com/images/steela/post/6539cc89-b1c5-4836-9d34-cb9835c25abc/image.png)

#### 4. index.html 안에 카카오 스크립트 추가
```
    <script src="https://developers.kakao.com/sdk/js/kakao.js"></script>
```

#### 5. 타입 지정
(참고: 
https://i-ten.tistory.com/318
https://velog.io/@jian09/d.ts-%ED%8C%8C%EC%9D%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0)

타입스크립트를 사용하고 있어 타입을 지정해주어야 한다.
KaKao 객체에 대해 Window 객체를 확장해주어 인터페이스로 정의해주어야 오류가 안난다고 하는데, KaKao 객체를 Main.tsx 페이지에서 사용할 때 계속 오류가 났다. 
@types/types.d.ts 파일에서 Window 인터페이스를 확장하는 대신, 모듈 확장을 사용하여 Kakao를 전역으로 선언하였다.

```
declare global {
  interface Window {
    Kakao: any;
  }
}

```




#### 6. 카카오 API 초기화
공유하기 기능이 들어갈 페이지는 맨 처음 메인페이지와, 결과 페이지가 될 것이다.
각 페이지에 useEffect로 카카오 API를 초기화 해준다.

```
  useEffect(() => {
    // SDK를 초기화 합니다. 사용할 앱의 JavaScript 키를 설정해야 합니다.
    if (!window.Kakao.isInitialized()) {
      window.Kakao.init(process.env.REACT_APP_KAKAO_KEY);
    }

    // SDK 초기화 여부를 판단합니다.
    console.log(window.Kakao.isInitialized());
  }, []);
```

#### 4. 메세지 - 카카오톡 공유
https://developers.kakao.com/docs/latest/ko/message/js-link
![](https://velog.velcdn.com/images/steela/post/8997faa5-54de-4925-bf66-a229e6bd07d3/image.png)


이 부분은 2편에서 이어서 작성하겠다.



