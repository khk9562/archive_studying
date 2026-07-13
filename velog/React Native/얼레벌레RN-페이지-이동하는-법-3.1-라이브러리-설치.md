---
title: "[얼레벌레RN] 페이지 이동 관련 라이브러리 설치 - 4"
tags: ["react native", "router"]
date: 2024-05-15
velog_id: 6528831e-78e5-45b0-bb55-c43bf384a279
velog_url: https://velog.io/@steela/얼레벌레RN-페이지-이동하는-법-3.1-라이브러리-설치
velog_updated: 2026-07-13T10:16:40.098Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-페이지-이동하는-법-3.1-라이브러리-설치](https://velog.io/@steela/얼레벌레RN-페이지-이동하는-법-3.1-라이브러리-설치) · 📅 2024-05-15

## 사담
UI를 이것저것 만져보다가 페이지 이동을 어떻게 하는지 문득 궁금해져 useNavigation 같은 훅으로 해보려고 하니 뭔가 React와 작동 방식이 다르다...!

> ** react web에서는 말 그대로 URL을 통한 페이지 → 페이지의 이동이었다면
앱에서는 스크린 간 이동이 아닌 자료구조의 스택과 같이 스크린을 쌓아갑니다. **
[참조] https://lasbe.tistory.com/172


이리저리 검색을 통해 바로 깨달은 것은 매우 많은 라이브러리 설치가 필요하다는 점...!
공식문서에서 바로 찾기가 좀 힘들어서 구글 검색을 통해 먼저 알아보았다.
블로그마다 너무 많은 라이브러리 설치를 필요로해서 일단은 공식문서를 우선으로 하고, 하나하나 알아보았다.


<br />

# 라이브러리 설치

#### 공식문서에서 권장하는 기본 설치 라이브러리(https://docs.expo.dev/router/installation/)



```
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

<br />

## 라이브러리 부가 설명

> #### 1. Stack Navigation을 설치합니다.
```
npx expo install expo-router
npm install @react-navigation/stack @react-navigation/native
```

StackNavigator안에 있는 모든 스크린 컴포넌트들은 navigation이라는 prop을 받게 된다. 이를 이용하여 페이지를 navigating 할 수 있다.

<br />

> #### 2. 안전 영역에 대한 정보를 제공하는 라이브러리를 설치한다.
```
npx expo install react-native-screens react-native-safe-area-context
```

navigation 라이브러리가 종속성을 갖고 있는 라이브러리 입니다.
미설치하면 "RNSScreenStackHeaderConfig" was not found in the UIManager. 빌드 에러가 발생합니다.

<br />

> #### 3. 제스쳐 핸들러 설치
```
npx expo install react-native-gesture-handler
```

이 라이브러리는 모바일 애플리케이션에서 제스처 기반의 상호작용을 쉽게 구현할 수 있도록 해줍니다. 예를 들어, 슬라이드 메뉴, 스와이프, 드래그 앤 드롭과 같은 제스처를 감지하고 처리하는 기능을 제공합니다. react-native-gesture-handler는 React Navigation과 함께 사용되며, 네비게이션 동작에 자연스러운 제스처를 추가하는 데 필수적입니다.

<br />

> #### 4. 스와이프하여 화면을 전환할 수 있는 뷰 페이저 기능을 제공
```
npx expo install react-native-pager-view
```

<br />

**_나는 일단 여기 4번까지만 설치했다... 라이브러리가 너무 많아서 아래는 참고만 하다가 필요시 설치 예정_**

<br />
<br />

> #### 5. 디자인 효과를 넣을 수 있도록 라이브러리 추가 
```
npm install --save @react-native-masked-view/masked-view
```

이 라이브러리는 뷰(View)에 마스크 효과를 적용할 수 있게 해줍니다. 즉, 특정 부분만을 표시하거나 숨기는 효과를 만들 때 사용됩니다. 예를 들어, 원형, 사각형, 별 모양 등 다양한 모양의 마스크를 적용하여 UI의 특정 부분만을 강조하거나 숨길 수 있습니다. 이러한 마스크 효과는 UI 디자인에서 시각적인 다양성과 창의성을 높이는 데 유용합니다.

> #### 6. 화면 하단에 위치하는 탭 바를 만드는 데 사용
```
npm install @react-navigation/bottom-tabs
```

> #### 7. 상단 탭 바를 구현
```
npm install @react-navigation/material-top-tabs react-native-tab-view
```







> ### 참조
https://docs.expo.dev/router/installation/
https://docs.expo.dev/router/advanced/stack/
https://angelpsyche.tistory.com/57
https://xionwcfm.tistory.com/440
https://adjh54.tistory.com/202
https://lucky516.tistory.com/282

