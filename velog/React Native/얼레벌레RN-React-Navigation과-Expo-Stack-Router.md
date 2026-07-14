---
title: "[얼레벌레RN] React Navigation과 Expo Stack Router"
tags: ["react native"]
date: 2024-05-30
velog_id: e0dca3e6-d6c4-4c21-a691-681d1d377b43
velog_url: https://velog.io/@steela/얼레벌레RN-React-Navigation과-Expo-Stack-Router
velog_updated: 2026-07-14T01:31:34.984Z
synced_at: 2026-07-14
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-React-Navigation과-Expo-Stack-Router](https://velog.io/@steela/얼레벌레RN-React-Navigation과-Expo-Stack-Router) · 📅 2024-05-30
라섹한지 6일차, 아직도 세상이 눈부셔서 밝은 곳에서 눈을 뜨기 힘들고 컴퓨터 화면도 실눈뜨면서 보는 수준이지만... 알고리즘 스터디 참여 중 문득 React 컨퍼런스 글을 보게 되고, 리액트 네이티브 관련 번역글을 보게 되었다.
그 과정에서 라우팅 방법을 검색해보다가 내가 기존에 하던 Expo router을 사용해서 스크린 스택을 쌓는 것이 아니라, React Navigation이라는 라이브러리를 별도로 사용해서 페이지 라우팅하는 방법을 블로그를 통해 알게 되었다.

### Expo Stack Router 방식
```
import { Stack } from "expo-router";

.
.
.

      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="+not-found" />
      </Stack>
```
![](https://velog.velcdn.com/images/steela/post/40934a8a-25f0-4ba7-a861-5e53a7551e7f/image.png)

페이지를 기존 프로젝트 구성시 탭 형식으로 되어있길래 그대로 사용하였고, 이게 name에서는 자동으로 작동되었다.
다만 React Navigation 라이브러리를 사용하는 과정에서는 자동으로 입력이 안되며 컴포넌트를 일일이 지정해주어야 한다.

### React Navigation
```
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import HomeScreen from "./(tabs)";

const Stack = createNativeStackNavigator();

      <NavigationContainer>
        <Stack.Navigator>
          <Stack.Screen component={HomeScreen} name="Home" />
        </Stack.Navigator>
      </NavigationContainer>
```

React Navigation 라이브러리는 컴포넌트로 직접 불러오는 식이며,
그 컴포넌트들 또한 일일이 import 해주어야 한다.


### 결론

Expo Router는 Expo로 구성한 프로젝트에서 페이지 기반으로 자동으로 라우팅해주는 편리성이 있지만,
React Navigation은 약간의 귀찮음이 있지만 내가 원하는 컴포넌트를 직접 불러올 수 있는 자유도가 더 크다고 느껴진다.
토이프로젝트와 같이 정말 간편한 프로젝트에는 전자가 적합하고, 이외에는 후자가 간편하지 않을까?라는 예상을 해본다, 이건 아마 리액트 네이티브 프로젝트 구성단계에서 React Native CLI와 Expo 를 선택하는 과정과도 같을 것으로 추측된다!

기본적인 기능 구현만 한거라서 개발 중에 다른 트러블이 생기면 추가 기록하겠다. (2024.05.30.)
