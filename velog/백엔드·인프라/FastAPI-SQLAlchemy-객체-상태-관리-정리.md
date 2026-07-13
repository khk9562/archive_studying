---
title: "[FastAPI] SQLAlchemy 객체 상태 관리 정리"
tags: ["FastAPI", "SQLAlchemy", "python"]
date: 2025-01-21
velog_id: d7a06795-7ebf-407c-a736-2d2e404bf9fe
velog_url: https://velog.io/@steela/FastAPI-SQLAlchemy-객체-상태-관리-정리
velog_updated: 2026-07-13T10:16:37.843Z
synced_at: 2026-07-13
---

> 🔗 원본: [velog.io/@steela/FastAPI-SQLAlchemy-객체-상태-관리-정리](https://velog.io/@steela/FastAPI-SQLAlchemy-객체-상태-관리-정리) · 📅 2025-01-21
얼레벌레 푸시 알림 구현 중에 푸시 알림 생성 혹은 읽음처리 시에 앱 뱃지 카운트에 반영이 안되는 버그를 발견했다. 왜 그럴까 디버깅 해보다가 db 최신 상태 반영이 안돼서 감지를 못하고 있었다는 사실을 확인. db.commit(), rollback()만 제대로 이해하고 있었는데 이번 기회에 나머지도 정리해본다.
<br/>
## SQLAlchemy란?
> SQL의 모든 기능과 유연성을 제공하는 Python SQL 툴킷 및 객체 관계형 매퍼. [공식 홈페이지](https://www.sqlalchemy.org/)

<br/>

### add
- session에 인스턴스 배치
- 다음 flush 때 INSERT 발생

<br/>

### flush
- 트랜잭션을 데이터베이스로 전송. 아직 커밋X
- 영속성 컨텍스트 내용을 데이터베이스에 동기화하는 작업
- 에러 발생 시 롤백이 가능한 단계까지만 반영함

<br />

### commit
- 작업 내용을 데이터베이스에 확정하는 작업으로, 해당 단계 이후에는 롤백이 불가능

<br/>

### rollback
- 데이터 복구

<br/>

### Refresh
- 객체의 최신 상태 데이터가 필요한 경우
- 스레드에서 변경이 되어 변경 이전 데이터를 가져온 경우, 함수를 실행하여 항상 최신 데이터 가져올 수 있음

<br/>

## 주의할 사항
- autoflush, autocommit이 어떻게 설정되어있느냐에 따라 함수 사용 및 처리 방법이 다름
- autoflush가 True라면 굳이 flush를 일일이 호출할 필요가 없음.
- commmit 도 마찬가지이지만, 한번 commit 되면 rollback 시킬 수 없기 때문에 autocommit은 False로 처리해놓을 것.


<br/>

### 참조
[SQLAlchemy add, flush, commit](https://jybaek.tistory.com/914)
[[FastAPI] SQLAlchemy 객체 상태 관리 (expire, refresh, flush, commit, rollback)](https://m.blog.naver.com/dev-blackcat/222588712961)
