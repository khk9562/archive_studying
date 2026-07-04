---
title: "문자열 내 p와 y의 개수"
tags: ["Level.1", "Python3", "프로그래머스"]
date: 2024-03-07
velog_id: af06f6f8-6d83-40fd-b8d8-ffdf2e01f276
velog_url: https://velog.io/@steela/문자열-내-p와-y의-개수
velog_updated: 2026-07-04T03:07:06.118Z
synced_at: 2026-07-04
---

> 🔗 원본: [velog.io/@steela/문자열-내-p와-y의-개수](https://velog.io/@steela/문자열-내-p와-y의-개수) · 📅 2024-03-07
#### 문제 설명
대문자와 소문자가 섞여있는 문자열 s가 주어집니다. s에 'p'의 개수와 'y'의 개수를 비교해 같으면 True, 다르면 False를 return 하는 solution를 완성하세요. 'p', 'y' 모두 하나도 없는 경우는 항상 True를 리턴합니다. 단, 개수를 비교할 때 대문자와 소문자는 구별하지 않습니다.

예를 들어 s가 "pPoooyY"면 true를 return하고 "Pyy"라면 false를 return합니다.

#### 제한사항
- 문자열 s의 길이 : 50 이하의 자연수
- 문자열 s는 알파벳으로만 이루어져 있습니다.

#### 첫번째 풀이

파이썬 문법을 모르겠어서 아는 걸로 대충 풀어봤다.
```
def solution(s):
    Li = list(s);
    p = len("p" in list(s));
    y = len("y" in list(s));
    print(p, y);
    answer = True
    

    return True
```

TypeError: object of type 'bool' has no len()

in 문법은 리스트 안에 문자가 있는지 없는지를 판별에 true/false를 리턴한다는 것을 알게 되었다.
그래서 리스트 안에서 문자열 개수 찾는 방법을 검색했다.

#### 두번째 풀이

검색 결과로, list.count() 함수를 쓰면 된다고 나왔다.

```
def solution(s):
    Li = list(s);
    p = Li.count("p");
    y = Li.count("y");
    print(p, y);
    answer = True
    

    return True
```

각각의 개수가 print 함수를 통해 잘 출력되는 것을 확인했다.

#### 세번째 풀이

해당하는 함수를 찾았으니 조건을 붙여 정답을 출력하겠다.
```
def solution(s):
    Li = list(s);
    p = Li.count("p");
    y = Li.count("y");
    
    if p == y :
        answer = True
    else :
        answer = False
        
    return answer
```

테스트 케이스는 통과했으나 제출 후 채점하기에서 실패되는 결과들이 출력되었다.


#### 마지막 풀이
```
def solution(s):
    Li = list(s.lower());
    p = Li.count("p");
    y = Li.count("y");
    
    if p == y :
        answer = True
    else :
        answer = False
        
    return answer
```

대문자와 소문자가 구별된다는 조건을 빼먹었다.. 문제를 잘 읽고 코드를 입력하자..
드디어 통과!
