---
title: "[Typescript] 리액트 개발자  관점에서 이해하는 Conditional Types"
tags: ["Conditional Types", "JavaScript", "React", "typescript"]
date: 2025-09-19
velog_id: 3cef1185-d6f3-4d37-afad-66266d709c29
velog_url: https://velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Conditional-Types
velog_updated: 2026-07-15T08:51:02.374Z
synced_at: 2026-07-15
---

> 🔗 원본: [velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Conditional-Types](https://velog.io/@steela/Typescript-리액트-개발자-관점에서-이해하는-Conditional-Types) · 📅 2025-09-19

# Conditional Types란?
"만약 A 타입이면 B 타입을, 아니면 C 타입을" 하는 타입 레벨의 if문
> 기본 문법: T extends U ? X : Y

<br />

### 1. 기본 개념
```
// 간단한 예시
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>;    // true
type Test2 = IsString<number>;    // false
type Test3 = IsString<"안녕">;    // true

// 실용적인 예시
type ApiResponse<T> = T extends string 
  ? { message: T; code: number }
  : { data: T; success: boolean };

type StringResponse = ApiResponse<string>;
// 결과: { message: string; code: number }

type DataResponse = ApiResponse<{name: string}>;  
// 결과: { data: {name: string}; success: boolean }
```
  
<br/>

### 2. Props 타입을 조건부로 만들기

```
// 버튼 컴포넌트 - loading일 때는 onClick 불필요
interface ButtonProps<T extends boolean = false> {
  children: React.ReactNode;
  loading?: T;
  // loading이 true면 onClick 선택사항, false면 필수
  onClick: T extends true ? never : () => void;
}

// 사용
function MyComponent() {
  return (
    <>
      {/* loading이 false일 때는 onClick 필수 */}
      <Button onClick={() => console.log('클릭!')}>
        일반 버튼
      </Button>
      
      {/* loading이 true일 때는 onClick 불필요 */}
      <Button loading={true}>
        로딩 중...
      </Button>
    </>
  );
}
```
  
<br/> 

### 3. API 호출 함수의 반환 타입 조건부 설정

```
// API 옵션에 따라 반환 타입이 달라지는 함수
interface FetchOptions {
  includeMetadata?: boolean;
  format?: 'json' | 'text';
}

type FetchResult<T extends FetchOptions> = 
  T['includeMetadata'] extends true
    ? { data: any; metadata: { total: number; page: number } }
    : T['format'] extends 'text' 
    ? string
    : any;

async function fetchData<T extends FetchOptions>(
  url: string, 
  options: T
): Promise<FetchResult<T>> {
  // 구현...
  return {} as FetchResult<T>;
}

// 사용
const result1 = await fetchData('/api/users', { includeMetadata: true });
// 타입: { data: any; metadata: { total: number; page: number } }

const result2 = await fetchData('/api/users', { format: 'text' });
// 타입: string

const result3 = await fetchData('/api/users', {});
// 타입: any
```

<br />

### 4. 폼 필드 타입을 조건부로 만들기

```
// 필드 타입에 따라 다른 속성을 가지는 폼 필드
type FormFieldProps<T extends 'text' | 'number' | 'select'> = {
  name: string;
  label: string;
  required?: boolean;
} & (T extends 'text' 
  ? { placeholder?: string; maxLength?: number }
  : T extends 'number'
  ? { min?: number; max?: number; step?: number }
  : T extends 'select'
  ? { options: Array<{value: string; label: string}> }
  : never
);

// 사용
function TextInput(props: FormFieldProps<'text'>) {
  // props.placeholder, props.maxLength 사용 가능
  return <input type="text" placeholder={props.placeholder} />;
}

function NumberInput(props: FormFieldProps<'number'>) {
  // props.min, props.max, props.step 사용 가능
  return <input type="number" min={props.min} max={props.max} />;
}

function SelectInput(props: FormFieldProps<'select'>) {
  // props.options 사용 가능
  return (
    <select>
      {props.options.map(option => 
        <option key={option.value} value={option.value}>
          {option.label}
        </option>
      )}
    </select>
  );
}
```

<br />

### 5. ✨**유틸리티 타입 만들기**

```
// null/undefined 제거하기
type NonNullable<T> = T extends null | undefined ? never : T;

type Test = NonNullable<string | null | undefined>; // string

// 함수인지 확인하기
type IsFunction<T> = T extends (...args: any[]) => any ? true : false;

type Test1 = IsFunction<() => void>;     // true
type Test2 = IsFunction<string>;         // false

// 배열에서 요소 타입 추출하기
type ArrayElement<T> = T extends (infer U)[] ? U : never;

type StringArray = ArrayElement<string[]>;        // string
type NumberArray = ArrayElement<number[]>;        // number

// Promise에서 값 타입 추출하기
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type UserPromise = UnwrapPromise<Promise<User>>;   // User
type PlainString = UnwrapPromise<string>;          // string
6. 실제 프로젝트에서 사용하는 고급 패턴
typescript// API 응답의 성공/실패에 따른 조건부 타입
type ApiResult<TData, TError = string> = 
  | { success: true; data: TData; error?: never }
  | { success: false; error: TError; data?: never };

// 사용
async function fetchUser(id: number): Promise<ApiResult<User, 'USER_NOT_FOUND' | 'SERVER_ERROR'>> {
  try {
    const user = await api.getUser(id);
    return { success: true, data: user };
  } catch (error) {
    return { success: false, error: 'SERVER_ERROR' };
  }
}

// 타입 가드와 함께 사용
const result = await fetchUser(1);
if (result.success) {
  console.log(result.data.name); // result.data는 User 타입
} else {
  console.log(result.error); // result.error는 'USER_NOT_FOUND' | 'SERVER_ERROR'
}
```
  
  
<br />

### 7. 컴포넌트의 조건부 Props

```
typescript// 모달 컴포넌트 - 타입에 따라 다른 props
type ModalProps<T extends 'confirm' | 'alert' | 'custom'> = {
  isOpen: boolean;
  onClose: () => void;
  title: string;
} & (T extends 'confirm'
  ? {
      message: string;
      onConfirm: () => void;
      onCancel?: () => void;
    }
  : T extends 'alert'
  ? {
      message: string;
    }
  : T extends 'custom'
  ? {
      children: React.ReactNode;
    }
  : never
);

// 사용
function App() {
  return (
    <>
      <Modal<'confirm'>
        isOpen={true}
        title="삭제 확인"
        message="정말 삭제하시겠습니까?"
        onConfirm={() => {}}
        onClose={() => {}}
      />
      
      <Modal<'alert'>
        isOpen={true}
        title="알림"
        message="저장되었습니다"
        onClose={() => {}}
      />
      
      <Modal<'custom'>
        isOpen={true}
        title="커스텀 모달"
        onClose={() => {}}
      >
        <div>커스텀 내용</div>
      </Modal>
    </>
  );
}
```
  

<br />

### 8.  ✨**실무에서 가장 많이 사용하는 패턴**
  
```
// 테이블 컬럼 정의에서 조건부 타입
interface Column<T> {
  key: keyof T;
  title: string;
  render?: (value: T[keyof T], record: T) => React.ReactNode;
  sortable?: boolean;
}

// 데이터 타입에 따라 적절한 렌더러 자동 선택
type SmartColumn<T, K extends keyof T> = Column<T> & {
  render: T[K] extends string 
    ? (value: string, record: T) => React.ReactNode
    : T[K] extends number
    ? (value: number, record: T) => React.ReactNode
    : T[K] extends boolean
    ? (value: boolean, record: T) => React.ReactNode
    : (value: T[K], record: T) => React.ReactNode;
};
```
  
<br />

>** 핵심 사용 시점**
Props가 조건부로 달라져야 할 때
API 응답이 옵션에 따라 달라질 때
타입 안전한 유틸리티 함수 만들 때
복잡한 폼이나 컴포넌트에서 타입별 다른 동작
라이브러리 만들 때 유연한 API 설계

