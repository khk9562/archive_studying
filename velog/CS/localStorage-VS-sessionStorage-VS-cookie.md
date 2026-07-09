---
title: "localStorage VS sessionStorage VS cookie"
tags: ["Web Storage", "cookie", "localstorage", "sessionStorage", "web"]
date: 2024-03-05
velog_id: 3c7d255e-c99e-44fe-9327-36c7d626a787
velog_url: https://velog.io/@steela/localStorage-VS-sessionStorage-VS-cookie
velog_updated: 2026-07-08T17:53:31.291Z
synced_at: 2026-07-09
---

> 🔗 원본: [velog.io/@steela/localStorage-VS-sessionStorage-VS-cookie](https://velog.io/@steela/localStorage-VS-sessionStorage-VS-cookie) · 📅 2024-03-05
### Web Storage (localStorage, sessionStorage) VS cookie

## localStorage
- 데이터를 브라우저에 **영구적**으로 저장
- 사용자의 로컬 스토리지에 데이터가 저장되며, 브라우저를 닫아도 데이터가 유지
- **도메인별**로 별도의 로컬 스토리지를 가짐
- JavaScript를 사용하여 데이터를 저장하고 검색할 수 있음
- 데이터의 크기 제한은 보통 5MB
- 주로 사용자의 기본 설정, 사용자 프로필, 장바구니 등 영구적으로 저장되어야 하는 데이터에 사용

## sessionStorage
- 데이터를 브라우저에 **임시**로 저장
- 사용자의 세션 스토리지에 데이터가 저장되며, 브라우저 세션이 종료되면 데이터도 함께 삭제됨
- **도메인별**로 별도의 세션 스토리지를 가짐
- JavaScript를 사용하여 데이터를 저장하고 검색할 수 있음
- 데이터의 크기 제한은 보통 5MB
- 주로 로그인 세션, 임시 데이터, 페이지 간의 데이터 교환 등에 사용됨

## cookie
- 데이터를 클라이언트의 웹 브라우저에 저장
- 사용자의 컴퓨터에 파일로 저장되며, 설정된 만료일까지 유지됩니다.
- **도메인별**로 별도의 쿠키를 가집니다.
- JavaScript, 서버 측 언어를 사용하여 데이터를 저장하고 검색할 수 있습니다.
- 데이터의 크기 제한은 4KB입니다.
- 주로 사용자의 로그인 정보, 사용자 추적, 선호 설정 등에 사용됩니다.
- **쿠키는 매번 서버로 전송됨**
- JavaScript, 서버 측 언어를 사용하여 데이터를 저장하고 검색할 수 있습니다.
- 데이터의 크기 제한은 4KB입니다.
- 주로 사용자의 로그인 정보, 사용자 추적, 선호 설정 등에 사용됩니다.
