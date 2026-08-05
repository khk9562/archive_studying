---
title: "[얼레벌레RN] 로컬 폰트 세팅 - 2"
tags: ["fonts", "reactnative"]
date: 2024-05-15
velog_id: de5f6b94-9d2e-44ce-98b5-b202524569b9
velog_url: https://velog.io/@steela/얼레벌레RN-로컬-폰트-세팅-2
velog_updated: 2026-08-05T08:03:47.798Z
synced_at: 2026-08-05
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-로컬-폰트-세팅-2](https://velog.io/@steela/얼레벌레RN-로컬-폰트-세팅-2) · 📅 2024-05-15
> ### 공식문서 참조
https://docs.expo.dev/develop/user-interface/fonts/


## 폰트 선택
먼저 적용하고 싶은 폰트를 다운받아 @assets/fonts 폴더에 파일을 넣어준다.
나는 [눈누](https://noonnu.cc/index)라는 사이트에서 Pretendard 폰트를 다운받아 Light, Regular, Medium, Bold, Black만 넣어주었다.

## 폰트 적용 환경 세팅

그리고 expo-font 라이브러리를 설치한다.
```
npm install expo-font
```

App.js 파일에 import 시켜주고, 아래 코드와 같이 각 폰트를 불러온다.
```
import { useFonts } from "expo-font";

export default function App() {
  const [fontsLoaded] = useFonts({
    Prentendard200: require("./assets/fonts/Pretendard-Light.otf"),
    Prentendard400: require("./assets/fonts/Pretendard-Regular.otf"),
    Prentendard500: require("./assets/fonts/Pretendard-Medium.otf"),
    Prentendard600: require("./assets/fonts/Pretendard-Bold.otf"),
    Prentendard800: require("./assets/fonts/Pretendard-Black.otf"),
  });
  
    return (...
```

## Text 태그에 폰트 적용

Text 태그 안에서 style 속성을 이용하여 폰트 유형을 선택할 수 있다!

**App.js**
```
    return (
      <Text style={{ fontFamily: "Prentendard200" }}>
        야호 아무튼 개발환경 세팅 끝!
      </Text>
    );
```

## 폰트 로드 전 처리

또한 폰트가 아직 로딩되지 않았을 때는 로딩 중 화면을 띄워줄 것이므로 아래 코드를 return 위에 작성해준다.
```
  if (!fontsLoaded) {
    return <AppLoading />;
  }
```

## 전체 코드
```
import { StatusBar } from "expo-status-bar";
import { StyleSheet, Text, View, Image, Pressable, Button } from "react-native";
import { SafeAreaProvider } from "react-native-safe-area-context";
import { useFonts } from "expo-font";
import AppLoading from "./components/AppLoading";

const catImg = require("./assets/images/cat-1.jpg");

export default function App() {
  const [fontsLoaded] = useFonts({
    Prentendard200: require("./assets/fonts/Pretendard-Light.otf"),
    Prentendard400: require("./assets/fonts/Pretendard-Regular.otf"),
    Prentendard500: require("./assets/fonts/Pretendard-Medium.otf"),
    Prentendard600: require("./assets/fonts/Pretendard-Bold.otf"),
    Prentendard800: require("./assets/fonts/Pretendard-Black.otf"),
  });

  if (!fontsLoaded) {
    return <AppLoading />;
  }

  return (
    <SafeAreaProvider>
      <View style={styles.container}>
        <View style={styles.imgCont}>
          <Image source={catImg} style={styles.image} />
        </View>
        <View style={styles.TxtCont}>
          <Text style={{ fontFamily: "Prentendard200" }}>
            야호 아무튼 개발환경 세팅 끝!
          </Text>
        </View>
        <View style={styles.buttonContainer}>
          <Pressable
            style={styles.button}
            onPress={() => alert("고양이 귀엽져?")}
          >
            <Text style={styles.buttonLabel}>확인</Text>
          </Pressable>
          <Pressable
            style={styles.button}
            onPress={() => alert("귀여운 고양일 두고 정말요..?")}
          >
            <Text style={styles.buttonLabel}>취소</Text>
          </Pressable>
        </View>
        <StatusBar style="auto" />
      </View>
    </SafeAreaProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "column",
    alignItems: "center",
    justifyContent: "center",
    width: "100%",
    height: "100%",
    backgroundColor: "lightgray",
  },
  imgCont: {
    flex: 1,
    marginTop: 80,
  },
  image: {
    width: 300,
    height: 200,
    objectFit: "cover",
    borderRadius: 8,
  },
  TxtCont: {
    flex: 1,
    backgroundColor: "#efefef",
    width: 300,
    borderRadius: 8,
    paddingTop: 15,
    paddingBottom: 15,
    paddingLeft: 18,
    paddingRight: 18,
  },
  buttonContainer: {
    width: 320,
    height: 120,
    marginVertical: 30,
    flexDirection: "column",
    gap: 5,
  },
  button: {
    backgroundColor: "#333",
    borderRadius: 10,
    width: "100%",
    height: "50%",
    alignItems: "center",
    justifyContent: "center",
  },
  buttonIcon: {
    paddingRight: 8,
  },
  buttonLabel: {
    color: "#fff",
    fontSize: 16,
  },
});

```
