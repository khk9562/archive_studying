---
title: "npm install 옵션"
tags: ["npm"]
date: 2024-06-01
velog_id: c8f29338-e1af-4225-acc4-8b2ee3160304
velog_url: https://velog.io/@steela/npm-install-옵션
velog_updated: 2026-07-14T15:15:26.195Z
synced_at: 2026-07-15
---

> 🔗 원본: [velog.io/@steela/npm-install-옵션](https://velog.io/@steela/npm-install-옵션) · 📅 2024-06-01
npm install 명령 사용 시 세부적인 옵션에 대해서 정확히 알고 사용하기 위해 기록한다.

### --save
--save 옵션은 package.json의 dependency 항목에 모듈을 추가한다는 의미로, npm@5 부터 --save 옵션을 기본 옵션으로 저장한다.

### -g
지역 설치가 default 값이며, 모든 프로젝트가 공통으로 사용할 경우 전역에 설치하기 위한 용도이다.
전역에 설치된 패키지는 OS에 따라 설치 장소가 다르다.


### -P (--save-prod)
dependencies에 패키지를 등록. 프로젝트가 배포 시 사용될 의존성 모듈을 정의하고 설치한다. (defalut)

### -D (--save-dev)
devDependencies에 패키지를 등록. 개발 단계에서만 사용하는 의존성 모듈을 정의하고 설치한다.


### -O (--save-optional) 
optionalDependencies에 패키지를 등록. 선택적 의존성 모듈읠 정의하고 설치한다.


### --no-save
dependencies에 패키지를 등록하지 않는다.

### -E (--save-exact)
dependencies에 패키지를 등록.npm의 기본 SemVer 연산자를 사용하는 대신 정확한 버전으로 설치한다.

### -B (--save-bundle)
bundleDependencies에 패키지를 등록. 번들로 묶을 패키지 의존성 모듈을 정의하고 설치한다.

<br />


> 출처
https://ahnsisters.tistory.com/16 [안시스터즈:티스토리]
