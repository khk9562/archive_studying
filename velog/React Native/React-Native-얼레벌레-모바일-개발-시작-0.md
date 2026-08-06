---
title: "[얼레벌레 RN] 모바일 개발 시작 - 0"
tags: ["Expo", "react native"]
date: 2024-05-15
velog_id: a070c1c1-9a5e-49a9-9d95-bf5abd943fad
velog_url: https://velog.io/@steela/React-Native-얼레벌레-모바일-개발-시작-0
velog_updated: 2026-08-06T01:56:07.885Z
synced_at: 2026-08-06
---

> 🔗 원본: [velog.io/@steela/React-Native-얼레벌레-모바일-개발-시작-0](https://velog.io/@steela/React-Native-얼레벌레-모바일-개발-시작-0) · 📅 2024-05-15
## 사담
사이드 프로젝트를 참가하려던 중,
모바일 앱을 만드는 프로젝트를 웹으로 착각하여 잘못 참여하였다.
인원이 힘들게 모인 탓에 하차하기엔 늦었고
React Native 공부에 대한 열망을 나도 갖고 있었기에
기획과 디자인이 이뤄지는 기간 동안 빡세게 React Native 공부를 하기로 결심했다.

옛날 옛적... 기본 React Native 환경 세팅을 해두었던 Repository를 클론해와서 이것저것 만져보기로 했다.

**package.json**
```
{
  "name": "todolist",
  "version": "1.0.0",
  "main": "node_modules/expo/AppEntry.js",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  },
  "dependencies": {
    "axios": "^1.6.8",
    "expo": "~51.0.6",
    "expo-cli": "^6.3.10",
    "expo-font": "^12.0.5",
    "expo-status-bar": "~1.12.1",
    "react": "18.2.0",
    "react-native": "^0.74.1",
    "react-native-dotenv": "^3.4.11"
  },
  "devDependencies": {
    "@babel/core": "^7.20.0"
  },
  "private": true
}

```


위와 같은 라이브러리를 설치해두었고,
npx expo start 명령을 실행하면
아이폰에서 expo 어플을 통해 바로 화면을 볼 수 있는 상태.


<br />


## 공식 문서 튜토리얼
https://docs.expo.dev/get-started/create-a-project/
일단 공식 문서를 따라해보겠다.
본격 공부 시작~

## 기본 환경 설정


### 새로운 Expo 프로젝트를 만들기


```
// 아래 명령어를 VS Code Terminal에서 실행 

npx create-expo-app@latest
```


### 개발 서버 시작 명령

```
// 아래 명령어를 VS Code Terminal에서 실행 

npx expo start
```

#### 파일 구조: root 폴더에서 app, assets, components, constants, hooks, scripts 폴더를 만들어 사용할 수 있다.


### 프로젝트 재설정

```
npm run reset-project
```

이 명령은 app 의 기존 파일을 app-example 로 이동한 다음, 새 index.tsx 파일을 사용하여 새 앱 디렉터리를 만든다.

