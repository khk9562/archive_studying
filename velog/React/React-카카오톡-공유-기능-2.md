---
title: "[React] 카카오톡 공유 기능 -2"
tags: ["React", "react 카카오톡 공유 4019 에러", "카카오톡 공유기능"]
date: 2024-05-10
velog_id: 52d1085e-1d3d-4b9e-a1ba-7c9fa62c11d7
velog_url: https://velog.io/@steela/React-카카오톡-공유-기능-2
velog_updated: 2026-07-16T20:43:27.119Z
synced_at: 2026-07-17
---

> 🔗 원본: [velog.io/@steela/React-카카오톡-공유-기능-2](https://velog.io/@steela/React-카카오톡-공유-기능-2) · 📅 2024-05-10
### 메세지 - 카카오톡 공유

> 참고
https://developers.kakao.com/docs/latest/ko/message/js-link
https://gallery-k.tistory.com/421

위 블로그와 공식 문서를 참조하여 일단 직접 버튼을 만들어 기능을 구현해보려한다.
따로 공유 버튼 컴포넌트를 만들어서 페이지에 import해주었다.

**@components/ShareKaKaoBtn.tsx**
```
export default () => {
  // 배포한 자신의 사이트
  const realUrl = "https://what-if-you-were-a-philosophy-student.vercel.app";
  // 로컬 주소 (localhost 3002)
  const resultUrl = "http://localhost:3002";

  // 재랜더링시에 실행되게 해준다.
  useEffect(() => {
    // init 해주기 전에 clean up 을 해준다.
    Kakao.cleanup();
    // 자신의 js 키를 넣어준다.
    Kakao.init(process.env.REACT_APP_KAKAO_KEY);
    // 잘 적용되면 true 를 뱉는다.
    console.log("잘 적용이 됐나요?", Kakao.isInitialized());
  }, []);

  const shareKakao = () => {
    Kakao.Share.sendDefault({
      objectType: "feed",
      content: {
        title: "철BTI",
        description: "나의 철학과 포지션을 확인해보세요!",
        imageUrl: { resultUrl } + "icons/philosopher.png",
        link: {
          mobileWebUrl: resultUrl,
        },
      },
      buttons: [
        {
          title: "나도 테스트 하러가기",
          link: {
            mobileWebUrl: resultUrl,
          },
        },
      ],
    });
  };

  return (
    <>
      <button
        type="button"
        className="grey-btn"
        onClick={() => {
          shareKakao();
        }}
      >
        {" "}
        카카오톡 공유하기{" "}
      </button>
    </>
  );
};
```

하지만 로컬 환경에서 실행해보니 **4019 에러**가 뜬다.
![](https://velog.velcdn.com/images/steela/post/d6903791-9e93-4a69-baf5-8723ba41ba0b/image.png)

도메인을 다 등록했는데 왜 안되는지 고민하다가...
바보같은 실수를 했다는 걸 깨달았다.
![](https://velog.velcdn.com/images/steela/post/f7e7a849-6d7c-4b46-9266-326de37ed8f5/image.png)

**포트번호를 3001만 입력해놓고 3002는 등록을 안했다. 잘된다..**


