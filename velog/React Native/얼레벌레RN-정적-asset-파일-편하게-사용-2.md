---
title: "[얼레벌레RN] 정적 asset 파일 편하게 사용 - 3"
tags: ["Assets", "reactnative"]
date: 2024-05-15
velog_id: f9fea2a8-9e74-444e-95c5-68325bfd3961
velog_url: https://velog.io/@steela/얼레벌레RN-정적-asset-파일-편하게-사용-2
velog_updated: 2026-07-19T21:11:18.742Z
synced_at: 2026-07-20
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-정적-asset-파일-편하게-사용-2](https://velog.io/@steela/얼레벌레RN-정적-asset-파일-편하게-사용-2) · 📅 2024-05-15
> ### 공식문서
https://docs.expo.dev/develop/user-interface/assets/

### 로컬 이미지 불러오기 - 1

기존에 assets 폴더 안의 정적 파일, 즉 이미지 등을 불러와서 사용하려면 require문 등을 사용해야 하는 번거로움이 있다.

```
const catImg = require("./assets/images/cat-1.jpg");

export default function App() {

  return (
    <SafeAreaProvider>
      <View style={styles.container}>
        <View style={styles.imgCont}>
          <Image source={catImg} style={styles.image} />
        </View>
        .
        .
        .
```


### 로컬 이미지 불러오기 - 2 : expo-asset 라이브러리
#### **이 방법은 이미지 불러오기에 실패했을 때 에러 제어가 가능하다.**

```
npx expo install expo-asset
```

expo-asset 라이브러리를 설치하고, 이미지를 불러올 파일 안에서 useAssets을 import한다.

```
import { View, Text, Image } from "react-native";
import { useAssets } from "expo-asset";

const AppLoading = () => {
  const [assets, error] = useAssets([require("../assets/icon.png")]);
  return (
    <View>
      <Image source={assets[0]} />
      <Text>로딩중</Text>
    </View>
  );
};

export default AppLoading;

```



