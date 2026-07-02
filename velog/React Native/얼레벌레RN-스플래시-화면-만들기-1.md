---
title: "[얼레벌레RN] 스플래시 화면 만들기 - 1"
tags: ["figma", "react native"]
date: 2024-05-15
velog_id: 7b6997b7-c41e-43e6-93a8-cda31ed9b957
velog_url: https://velog.io/@steela/얼레벌레RN-스플래시-화면-만들기-1
velog_updated: 2026-07-01T21:20:57.975Z
synced_at: 2026-07-02
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-스플래시-화면-만들기-1](https://velog.io/@steela/얼레벌레RN-스플래시-화면-만들기-1) · 📅 2024-05-15
> ## **Splash screen**
https://docs.expo.dev/develop/user-interface/splash-screen/
Learn how to create a splash screen for your Expo project and other best practices.

문서 속에서 처음 앱을 열 때 등장하는 스플래시 화면을 만드는 과정이 있다.
<br />

> #### **Expo App Icon & Splash **
https://www.figma.com/community/file/1155362909441341285

참고로 위 사이트로 이동해서 Open In Figma 버튼을 누르면
Expo에서 제공하는 피그마 작업물을 열람 및 편집할 수 있다.
@assets 폴더 안에 splash.png 이미지가 있는데, 이게 스플래시 화면으로 등장하는 것이다.

![](https://velog.velcdn.com/images/steela/post/766a96db-6736-480d-9fa8-23c5fc00c20b/image.png)

여기서 splash를 편집하여 현재 스플래시 이미지 대체하겠다.

![](https://velog.velcdn.com/images/steela/post/100a2424-1c04-45d6-88ef-26f18318cc16/image.png)

우측 아래 export를 눌러 splash.png로 파일을 다운받아
assets 폴더 안의 파일과 교체해주었다.

매우 간단!

실행해보니 저세상 디자인 스플래시 화면이 성공적으로 출력된다.
![](https://velog.velcdn.com/images/steela/post/1a8cc9c8-9670-4f84-97d2-34ee5cafc598/image.jpeg)


<br />

**app.json**

```
{
  "expo": {
    "splash": {
      "image": "./assets/splash.png",
      "backgroundColor": "#FEF9B0"
    }
  }
}

```

이런 식으로 아이콘만 넣어주고 배경은 비워놓은 png 이미지를 사용했다면
자유자재로 배경색도 변경 가능하다고 한다.

