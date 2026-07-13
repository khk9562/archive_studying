---
title: "[Typescript] typeof 연산자 유용하게 사용하는 법"
tags: ["JavaScript", "React", "typescript"]
date: 2025-09-19
velog_id: 4ecac836-ee4e-4855-a73d-3b65907dd344
velog_url: https://velog.io/@steela/Typescript-typeof-연산자-유용하게-사용하는-법
velog_updated: 2026-07-13T10:16:39.241Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/Typescript-typeof-연산자-유용하게-사용하는-법](https://velog.io/@steela/Typescript-typeof-연산자-유용하게-사용하는-법) · 📅 2025-09-19
타입스크립트 공식문서 읽던 중에 typeof 연산자의 사용이 용이한 경우가 이해가 안돼서 Claude와 대화 후 리액트 개발자 관점에서 typeof 연산자가 유용한 실용적인 예시를 정리하려 한다.


### 1. 기존 객체에서 타입 추출하기
```
// 이미 작성된 설정 객체가 있다고 가정
const appConfig = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3,
  features: {
    darkMode: true,
    notifications: false
  }
} as const;


// typeof로 이 객체의 타입을 추출!
type AppConfig = typeof appConfig;
// 결과: 
{
  readonly apiUrl: "https://api.example.com";
  readonly timeout: 5000;
   readonly retries: 3;
   readonly features: {
     readonly darkMode: true;
     readonly notifications: false;
  };
 }
 
// 이제 다른 곳에서 같은 구조의 타입이 필요할 때
function updateConfig(newConfig: Partial<AppConfig>) {
  // ...
}
```

<br />

### 2. 함수의 반환 타입 추출 (ReturnType과 함께)
```
// API 호출 함수
async function fetchUserData(id: number) {
  const response = await fetch(`/api/users/${id}`);
  return {
    user: await response.json(),
    timestamp: Date.now(),
    cached: false
  };
}

// 이 함수의 반환 타입을 추출
type FetchResult = ReturnType<typeof fetchUserData>;
// 결과: Promise<{user: any; timestamp: number; cached: boolean}>

// Promise 안의 타입만 원한다면
type UserDataResult = Awaited<ReturnType<typeof fetchUserData>>;
// 결과: {user: any; timestamp: number; cached: boolean}

// useState에서 활용
const [userData, setUserData] = useState<UserDataResult | null>(null);
```

<br />

  
### 3. 컴포넌트 Props 타입 추출

```
// 기존 컴포넌트
function UserCard({ name, age, email, isActive }: {
  name: string;
  age: number;
  email: string;
  isActive: boolean;
}) {
  return <div>{name}</div>;
}

// 이 컴포넌트의 Props 타입 추출
type UserCardProps = React.ComponentProps<typeof UserCard>;
// 결과: {name: string; age: number; email: string; isActive: boolean}

// 다른 컴포넌트에서 재사용
function UserList({ users }: { users: UserCardProps[] }) {
  return (
    <div>
      {users.map(user => <UserCard key={user.email} {...user} />)}
    </div>
  );
}
```

<br />

### 4. Enum이나 상수 객체에서 타입 만들기
```
// 상태 상수 객체
const LoadingState = {
  IDLE: 'idle',
  LOADING: 'loading',
  SUCCESS: 'success',
  ERROR: 'error'
} as const;

// 이 값들을 타입으로 사용
type LoadingStateType = typeof LoadingState[keyof typeof LoadingState];
// 결과: "idle" | "loading" | "success" | "error"

// useState에서 사용
const [status, setStatus] = useState<LoadingStateType>('idle');

// 함수에서 사용
function handleStateChange(newState: LoadingStateType) {
  setStatus(newState); // 자동완성 지원!
}
```

<br />
  

### 5. ✨**라이브러리 타입 추출**

```
// 외부 라이브러리에서 가져온 함수
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'user',
  initialState: { name: '', age: 0 },
  reducers: {
    setName: (state, action) => {
      state.name = action.payload;
    }
  }
});

// 이 slice의 액션 타입들 추출
type UserActions = ReturnType<typeof userSlice.actions.setName>;
type UserState = ReturnType<typeof userSlice.getInitialState>;
```
  
  
<br />

### 6. ✨✨**API 응답 타입**
```
// 목업 데이터나 실제 API 응답
const mockUserResponse = {
  data: {
    id: 1,
    name: "김철수",
    profile: {
      avatar: "https://...",
      bio: "개발자입니다"
    },
    posts: [
      { id: 1, title: "첫 포스트", likes: 10 }
    ]
  },
  meta: {
    total: 100,
    page: 1
  }
} as const;

// 이것을 타입으로 추출
type UserResponse = typeof mockUserResponse;
type User = UserResponse['data'];
type UserProfile = User['profile'];
type Post = User['posts'][0];

// 컴포넌트에서 사용
interface UserProfileProps {
  user: User;
}

function UserProfile({ user }: UserProfileProps) {
  return (
    <div>
      <img src={user.profile.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>{user.profile.bio}</p>
    </div>
  );
}
```

<br />

### 7. 제네릭과 조합해서 사용
```
// 범용 API 래퍼 함수
function createApiCall<T>(mockData: T) {
  return async (): Promise<T> => {
    // 실제로는 API 호출
    return mockData;
  };
}

const getUserApi = createApiCall({
  id: 1,
  name: "김철수",
  email: "<email>"
});

// 이 함수의 반환 타입에서 데이터 타입 추출
type GetUserResponse = Awaited<ReturnType<typeof getUserApi>>;
// 결과: {id: number; name: string; email: string}
핵심 사용 시점

```

<br/>

> 타입을 직접 정의하기 귀찮을 때 - 기존 값에서 추출
외부 라이브러리 타입이 복잡할 때 - 필요한 부분만 추출
API 응답 구조를 타입으로 만들 때 - 목업 데이터에서 추출
다른 컴포넌트의 Props를 재사용할 때
상수 값들을 Union 타입으로 만들 때

#### => typeof는 "이미 있는 JavaScript 값의 타입을 TypeScript 타입으로 추출해주는" 도구!
