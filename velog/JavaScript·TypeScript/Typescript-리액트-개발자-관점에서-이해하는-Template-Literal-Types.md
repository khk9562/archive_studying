---
title: "[Typescript] 리액트 개발자 관점에서 이해하는 Template Literal Types"
tags: ["JavaScript", "React", "Template Literal Types", "typescript"]
date: 2025-09-19
velog_id: a51bcc76-9e08-434f-8023-874bd259a84e
velog_url: https://velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Template-Literal-Types
velog_updated: 2026-07-07T22:01:50.061Z
synced_at: 2026-07-08
---

> 🔗 원본: [velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Template-Literal-Types](https://velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Template-Literal-Types) · 📅 2025-09-19
타입스크립트 공부 중, 자바스크립트/리액트에서 그냥 사용하던 템플릿 리터럴은 타입 정의를 어떤 식으로 해야 효율적인지 궁금해서 Claude와의 얘기 후 내용을 정리해봤다.

# Template Literal Types란?
JavaScript의 템플릿 리터럴과 비슷하지만, 타입 레벨에서 문자열을 조합할 수 있는 기능

> 기본 문법: `${타입1}${타입2}`

<br />

### 1. 기본 개념

```
// 기본 사용법
type Greeting = `Hello, ${string}!`;

// 이제 이런 문자열들이 유효해요
const greeting1: Greeting = "Hello, 김철수!";  // OK
const greeting2: Greeting = "Hello, World!";   // OK
// const greeting3: Greeting = "Hi there!";    // Error!

// 유니온과 함께 사용
type Color = "red" | "blue" | "green";
type Size = "small" | "large";
type ClassName = `${Color}-${Size}`;
// 결과: "red-small" | "red-large" | "blue-small" | "blue-large" | "green-small" | "green-large"
```

<br/>


### 2. CSS 클래스명 타입 안전하게 만들기
```
// Tailwind CSS 스타일의 클래스명
type Colors = "red" | "blue" | "green" | "yellow";
type Shades = "100" | "300" | "500" | "700" | "900";
type Properties = "bg" | "text" | "border";

type TailwindClass = `${Properties}-${Colors}-${Shades}`;
// 결과: "bg-red-100" | "bg-red-300" | ... | "border-yellow-900" 등 모든 조합

// 컴포넌트에서 사용
interface ButtonProps {
  className?: TailwindClass;
  children: React.ReactNode;
}

function Button({ className, children }: ButtonProps) {
  return (
    <button className={className}>
      {children}
    </button>
  );
}

// 사용 - 자동완성과 타입 체크!
<Button className="bg-blue-500">클릭</Button>  // OK
<Button className="text-red-300">텍스트</Button>  // OK
// <Button className="bg-purple-500">버튼</Button>  // Error! purple는 정의되지 않음
```

<br />

### 3. ✨**이벤트 핸들러 네이밍**
```
// 폼 필드명에서 이벤트 핸들러명 자동 생성
type FormFields = "name" | "email" | "password" | "confirmPassword";
type EventHandler<T extends string> = `on${Capitalize<T>}Change`;

// 결과: "onNameChange" | "onEmailChange" | "onPasswordChange" | "onConfirmPasswordChange"
type FormHandlers = EventHandler<FormFields>;

interface FormProps {
  // 각 필드별 변경 핸들러
  onNameChange: (value: string) => void;
  onEmailChange: (value: string) => void;
  onPasswordChange: (value: string) => void;
  onConfirmPasswordChange: (value: string) => void;
}

// 더 동적으로 만들기
type FormHandlerMap<T extends string> = {
  [K in T as `on${Capitalize<K>}Change`]: (value: string) => void;
};

type MyFormHandlers = FormHandlerMap<FormFields>;
// 결과: {
//   onNameChange: (value: string) => void;
//   onEmailChange: (value: string) => void;
//   onPasswordChange: (value: string) => void;
//   onConfirmPasswordChange: (value: string) => void;
// }
```

<br />

### 4. API 엔드포인트 타입 정의

```
// REST API 엔드포인트
type Resource = "users" | "posts" | "comments";
type Method = "GET" | "POST" | "PUT" | "DELETE";
type Endpoint<R extends Resource, M extends Method> = `${M} /api/${R}`;

type UserEndpoints = Endpoint<"users", Method>;
// 결과: "GET /api/users" | "POST /api/users" | "PUT /api/users" | "DELETE /api/users"

// API 클라이언트에서 사용
interface ApiClient {
  request<T>(endpoint: Endpoint<Resource, Method>): Promise<T>;
}

const api: ApiClient = {
  async request(endpoint) {
    // 구현...
    return {} as any;
  }
};

// 사용 - 타입 안전한 API 호출
const users = await api.request<User[]>("GET /api/users");     // OK
const newUser = await api.request<User>("POST /api/users");    // OK
// const invalid = await api.request("GET /api/invalid");     // Error!
```

<br />

### 5. 환경 변수 타입 정의
```
// 환경 변수 접두사
type EnvPrefix = "REACT_APP" | "NEXT_PUBLIC";
type EnvSuffix = "API_URL" | "APP_NAME" | "DEBUG_MODE";
type EnvVar = `${EnvPrefix}_${EnvSuffix}`;

// 결과: "REACT_APP_API_URL" | "REACT_APP_APP_NAME" | ... 등

interface ProcessEnv {
  [K in EnvVar]?: string;
}

// 사용
declare global {
  namespace NodeJS {
    interface ProcessEnv extends ProcessEnv {}
  }
}

// 이제 타입 안전하게 환경 변수 사용
const apiUrl = process.env.REACT_APP_API_URL;     // OK
const appName = process.env.NEXT_PUBLIC_APP_NAME; // OK
// const invalid = process.env.INVALID_VAR;        // Error!
6. 데이터베이스 쿼리 빌더
typescript// 테이블명과 액션 조합
type Tables = "users" | "posts" | "comments";
type Actions = "find" | "create" | "update" | "delete";
type QueryMethod = `${Actions}${Capitalize<Tables>}`;

// 결과: "findUsers" | "createUsers" | "updateUsers" | ... 등

interface QueryBuilder {
  findUsers(): Promise<User[]>;
  createUsers(data: Partial<User>): Promise<User>;
  updateUsers(id: number, data: Partial<User>): Promise<User>;
  deleteUsers(id: number): Promise<void>;
  findPosts(): Promise<Post[]>;
  createPosts(data: Partial<Post>): Promise<Post>;
  // ... 등등
}

// 동적으로 메서드 타입 생성
type QueryMethods<T extends Tables, A extends Actions> = {
  [K in A as `${K}${Capitalize<T>}`]: K extends "find" 
    ? () => Promise<any[]>
    : K extends "create"
    ? (data: any) => Promise<any>
    : K extends "update"
    ? (id: number, data: any) => Promise<any>
    : K extends "delete"
    ? (id: number) => Promise<void>
    : never;
};
```

<br />

### 7. 라우팅 패스 타입
```
// Next.js 스타일의 동적 라우팅
type Route = 
  | "/"
  | "/about"  
  | "/users"
  | `/users/${string}`
  | "/posts"
  | `/posts/${string}`
  | `/posts/${string}/edit`;

// 라우터 함수
function navigate(path: Route) {
  // 구현...
}

// 사용
navigate("/");                    // OK
navigate("/users");               // OK  
navigate("/users/123");           // OK
navigate("/posts/abc/edit");      // OK
// navigate("/invalid");          // Error!

// 더 동적으로 만들기
type DynamicRoute<T extends string> = T | `${T}/${string}`;
type UserRoutes = DynamicRoute<"/users">;   // "/" | "/users" | "/users/${string}"
```

<br />

### 8. CSS-in-JS 스타일 타입

```
// styled-components나 emotion에서 유용
type CSSUnit = "px" | "rem" | "em" | "%" | "vh" | "vw";
type CSSValue<T extends number> = `${T}${CSSUnit}`;

type Spacing = CSSValue<0 | 4 | 8 | 12 | 16 | 20 | 24>;
// 결과: "0px" | "0rem" | "4px" | "4rem" | ... 등

interface StyledProps {
  margin?: Spacing;
  padding?: Spacing;
  width?: Spacing;
  height?: Spacing;
}

// 사용
const StyledDiv = styled.div<StyledProps>`
  margin: ${props => props.margin};
  padding: ${props => props.padding};
`;

<StyledDiv margin="16px" padding="8rem" />  // OK
// <StyledDiv margin="15px" />              // Error! 15는 허용되지 않음
```

### 9. 타입 안전한 i18n

```
// 다국어 키 타입
type Language = "ko" | "en" | "ja";
type TranslationKey = "welcome" | "goodbye" | "error.notFound" | "button.save";

type I18nKey<L extends Language, K extends TranslationKey> = `${L}.${K}`;

// 모든 조합
type AllI18nKeys = I18nKey<Language, TranslationKey>;
// 결과: "ko.welcome" | "ko.goodbye" | ... | "ja.button.save" 등

interface TranslationStore {
  [K in AllI18nKeys]: string;
}

const translations: TranslationStore = {
  "ko.welcome": "환영합니다",
  "ko.goodbye": "안녕히가세요",
  "ko.error.notFound": "찾을 수 없습니다",
  "ko.button.save": "저장",
  "en.welcome": "Welcome",
  "en.goodbye": "Goodbye",
  "en.error.notFound": "Not found",
  "en.button.save": "Save",
  // 모든 키를 반드시 정의해야 함
};

function t(key: AllI18nKeys): string {
  return translations[key];
}

// 사용 - 자동완성 지원!
const message = t("ko.welcome");  // OK
// const invalid = t("ko.invalid");  // Error!
```

<br />


> **핵심 사용 시점**
CSS 클래스명이나 스타일 값을 타입 안전하게 만들 때
API 엔드포인트나 라우팅 경로 정의할 때
이벤트 핸들러나 메서드명을 동적으로 생성할 때
환경 변수나 설정값 키를 타입화할 때
다국어 키나 데이터베이스 쿼리 메서드 정의할 때
