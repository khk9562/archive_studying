---
title: "[얼레벌레RN] Safe Area 확보 - 2"
tags: ["react-native-safe-area-context", "reactnative"]
date: 2024-05-15
velog_id: 13979317-2d56-4a0e-b7c2-db72b55cef70
velog_url: https://velog.io/@steela/얼레벌레RN-Safe-Area-확보-2
velog_updated: 2026-07-07T19:38:09.700Z
synced_at: 2026-07-08
---

> 🔗 원본: [velog.io/@steela/얼레벌레RN-Safe-Area-확보-2](https://velog.io/@steela/얼레벌레RN-Safe-Area-확보-2) · 📅 2024-05-15
> ### 공식문서 튜토리얼
https://docs.expo.dev/develop/user-interface/safe-areas/



**모바일의 경우, 상태 표시줄, 노치, 모서리 등에 의해 기기별로 다르게 화면이 가려진다. 이에 대응하기 위해 위 라이브러리를 설치하고, 아래와 같이 사용한다.**

```
npx expo install react-native-safe-area-context
```

**App.js**

```
import { View, Text } from 'react-native';
import { SafeAreaProvider } from 'react-native-safe-area-context';

export default function App() {
  return (
    <SafeAreaProvider>
      <View>
        <Text>My App</Text>
      </View>
    </SafeAreaProvider>
  );
}

```

App.js에서 사용해야 모든 페이지에 공통적인 Safe Area를 사용할 수 있다.
