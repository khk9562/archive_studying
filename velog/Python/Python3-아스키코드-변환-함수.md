---
title: "[Python3] 아스키코드 변환 함수"
tags: ["ASCII", "Python3", "아스키"]
date: 2024-05-14
velog_id: 310c22b9-5d40-4a42-b724-8145c003a908
velog_url: https://velog.io/@steela/Python3-아스키코드-변환-함수
velog_updated: 2026-07-03T01:50:44.809Z
synced_at: 2026-07-03
---

> 🔗 원본: [velog.io/@steela/Python3-아스키코드-변환-함수](https://velog.io/@steela/Python3-아스키코드-변환-함수) · 📅 2024-05-14

## 아스키 코드(ASCII)란?

미국정보교환표준부호(영어: American Standard Code for Information Interchange), 또는 줄여서 ASCII( /ˈæski/, 아스키)는 영문 알파벳을 사용하는 대표적인 문자 인코딩이다. 아스키는 컴퓨터와 통신 장비를 비롯한 문자를 사용하는 많은 장치에서 사용되며, 대부분의 문자 인코딩이 아스키에 기초를 두고 있다.

아스키는 7비트 인코딩으로, 33개의 출력 불가능한 제어 문자들과 공백을 비롯한 95개의 출력 가능한 문자들로 총128개로 이루어진다. 제어 문자들은 역사적인 이유로 남아 있으며 대부분은 더 이상 사용되지 않는다. 출력 가능한 문자들은 52개의 영문 알파벳 대소문자와, 10개의 숫자, 32개의 특수 문자, 그리고 하나의 공백 문자로 이루어진다.

> (참조) ASCII - 위키백과
https://ko.wikipedia.org/wiki/ASCII

아스키코드 테이블은 아래와 같다.
![](https://velog.velcdn.com/images/steela/post/8718d5f3-38df-4194-8035-2f3772d90f98/image.png)

> (참조) 아스키코드 - 나무위키
https://namu.wiki/w/%EC%95%84%EC%8A%A4%ED%82%A4%20%EC%BD%94%EB%93%9C

<br />


## 아스키코드 변환 함수

### ord(문자)
입력받은 문자를 아스키코드 테이블에서 보여지는 아스키코드 값을 출력한다.



### chr(숫자)
입력받은 숫자를 아스키코드 테이블에서 보여지는 아스키코드 값을 출력한다.

> (참조) velog
https://velog.io/@g0garden/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%AC%B8%EC%9E%90-%EC%95%84%EC%8A%A4%ED%82%A4%EC%BD%94%EB%93%9C%EB%A1%9C-%EB%B3%80%ED%99%98
