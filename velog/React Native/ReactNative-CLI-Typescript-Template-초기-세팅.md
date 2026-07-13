---
title: "[RN CLI] ReactNative CLI + Typescript Template 초기 세팅"
tags: ["Typescript Template", "cli", "reactnative"]
date: 2024-06-16
velog_id: 6f4550a8-b247-4e42-909b-6693bcf29068
velog_url: https://velog.io/@steela/ReactNative-CLI-Typescript-Template-초기-세팅
velog_updated: 2026-07-13T10:16:46.105Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/ReactNative-CLI-Typescript-Template-초기-세팅](https://velog.io/@steela/ReactNative-CLI-Typescript-Template-초기-세팅) · 📅 2024-06-16
사이드 프로젝트 진행을 Expo가 아닌 CLI로 하기로 결정했다. 초기 환경 세팅에 큰 번거로움과 어려움을 느껴서 이 글을 통해 나와 같은 시행착오를 겪지 않길 바란다...




## Node와 Watchman 설치

```bash
brew install node
brew install watchman
```

**설치 확인 명령어**
```bash
node -v
watchman --version
```

참고로 node 22.3.0 버전을 사용하다가 npm run ios를 통해 실행하면 
```
(node:6169) [DEP0040] DeprecationWarning: The punycode module is deprecated. Please use a userland alternative instead.
(Use node --trace-deprecation ... to show where the warning was created)
```

실행은 정상적으로 되지만 터미널에서 빨간 글씨로 이 경고 문구가 계속 출력되었다.
이게 거슬려서 나는 nvm으로 node의 버전을 18.18.2로 downgrade 해주었다.

<br />

## ReactNative CLI 설치
여기서 유의할 점은, 글로벌로 설치한 적이 있다면 그걸 먼저 반드시 삭제하고 재설치해야한다.

```bash
// 기존 설치 제거
npm uninstall -g react-native-cli
 
// 설치 명령어
npm install -g react-native-cli
 
// 권한이 없다는 오류가 뜬다면 sudo 명령어를 사용
sudo npm install -g react-native-cli
```

<br />

## ReactNative CLI + Typescript Template 설치

```bash
npx react-native init 프로젝트이름 --template react-native-template-typescript
```
나는 여기서 과거 블로그 글에서 어줍잖게 주워온 버전의 템플릿을 설치했더니 nodemodules만 세팅되는 이슈가 있었다... typescript 뒤의 @6.11.11 과 같은 버전을 생략하고 설치하는 걸 권장드린다.

<br />
<br />

> ### 참고
**[리액트 네이티브 공식문서 - iOS 환경 세팅] **
https://reactnative.dev/docs/set-up-your-environment?platform=ios
