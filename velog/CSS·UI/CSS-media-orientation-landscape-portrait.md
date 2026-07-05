---
title: "[CSS] @media orientation: landscape / portrait"
tags: []
date: 2024-06-18
velog_id: b12092ea-b4d7-4f3f-8974-52007c72c632
velog_url: https://velog.io/@steela/CSS-media-orientation-landscape-portrait
velog_updated: 2026-07-05T06:44:23.658Z
synced_at: 2026-07-05
---

> 🔗 원본: [velog.io/@steela/CSS-media-orientation-landscape-portrait](https://velog.io/@steela/CSS-media-orientation-landscape-portrait) · 📅 2024-06-18
스크린 가로, 세로 모드를 CSS로 컨트롤 할 수 있다는 사실은 알았지만 구체적인 속성은 모르고 있었다.
2024년도에 변화된 CSS 속성을 찾아보던 중, orientation을 알게되었다.

```bash

@media screen and (orientation: landscape) {
  /* 스크린이 가로일 떄 스타일 적용 */
}

@media screen and (orientation: portrait) {
  /* 스크린이 세로(가로 길이가 세로 길이만큼 짧아진 시점 이후)일 때만 스타일 적용 */
}

```

결론만 설명하자면,

orientation의 속성값으로는 2가지가 있다.
- **landscape** : 화면이 가로일 때 스타일을 설정
- **portrait** : 화면이 세로일 때 스타일 설정

가로, 세로를 구분하는 기준은 화면의 가로, 세로 길이이다.
스크린은 기본적으로 가로라고 생각되므로, 세로 일 때의 속성인 portrait로 설명하자면
스크린의 가로 길이가 세로 길이만큼 짧아진 시점일 때 portrait 속성에 설정한 스타일이 적용된다.
이는 가로가 500px, 세로가 500px일 때도 portrait 속성의 스타일이 적용된다는 말이다.

잘 활용해보자!
