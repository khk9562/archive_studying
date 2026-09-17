---
title: "Docker 이미지 번들 배포 때 Build Cache 와 Volume 에서 일어나는 일"
tags: ["CS"]
date: 2026-09-17
notion_id: 3de922cf-26a8-817b-9d96-e3f65cdd350c
notion_last_edited: 2026-09-17T08:24:00.000Z
synced_at: 2026-09-17
---
> 📅 **학습일**: 2026-09-17

> 🎯 **왜 정리했나**: Docker Desktop 용량이 50GB 넘게 불어 있어서 정리하다 보니, 배포 명령 한 번에 Containers·Images 말고 **Build Cache 와 Volumes 에서는 무슨 일이 일어나는지** 제대로 모르고 있었다. 개발 PC 에서 이미지를 빌드해 tar 로 묶고, 서버에서 load 해서 compose 로 올리는 **오프라인(번들) 배포** 흐름 기준으로 정리.


# 1. 전체 흐름 한눈에


| 단계                                       | 어디서     | Build Cache | Volumes                      |
| ---------------------------------------- | ------- | ----------- | ---------------------------- |
| ① 이미지 빌드 (`docker compose build --pull`) | 개발 PC   | **여기서 쌓인다** | 변화 없음                        |
| ② 이미지 묶기 (`docker save | gzip`)          | 개발 PC   | 변화 없음       | 변화 없음                        |
| ③ 전송 (scp 등)                             | PC → 서버 | —           | —                            |
| ④ 적용 (`docker load` → `compose up -d`)   | 서버      | —           | **기존 named volume 에 다시 붙는다** |


한 줄 요약: **빌드 찌꺼기는 빌드한 곳(PC)에, 데이터는 실행하는 곳(서버)의 볼륨에** 남는다.


# 2. Build Cache — 개발 PC


### 무엇이 캐시로 남나

- 멀티스테이지 Dockerfile 이라면 보통 이렇게 생겼다.

```docker
FROM golang:1.26-alpine AS builder   # 빌드 단계: 의존성 다운로드 + 컴파일
...
FROM node:24-alpine AS web-builder   # 빌드 단계: npm/pnpm install + 번들링
...
FROM alpine:3.21                     # 실행 단계: 결과물만 복사
COPY --from=builder /app/server /server
```

- 최종 이미지에는 마지막 `alpine` 단계만 들어간다. 그런데 **빌드 단계의 레이어(모듈 다운로드, node_modules, 컴파일 중간 산출물, golang·node 베이스 이미지)는 전부 BuildKit 빌드 캐시에 남는다.**
- 그래서 `docker images` 에는 golang·node 이미지가 안 보이는데 `docker system df` 의 **Build Cache 가 수십 GB** 로 잡힌다.

### 왜 계속 불어나나

- 커밋마다 소스가 바뀌니 `COPY . .` 이후 컴파일 레이어가 매번 새로 생긴다. 옛 레이어는 지워지지 않고 남는다.
- `--pull` 로 베이스 이미지가 갱신되면 그 위에 쌓인 레이어도 새로 만들어진다.
- 서버가 x86_64 인데 PC 가 Apple Silicon(arm64)이면 `DOCKER_DEFAULT_PLATFORM=linux/amd64` 로 **에뮬레이션 빌드**를 한다. 느리고, 캐시도 amd64 판본으로 따로 쌓인다.
- 배포 스크립트가 옛 이미지 태그나 번들 파일은 정리해도 **빌드 캐시까지 지우는 경우는 드물다.** 직접 치워야 한다.

### 정리


```bash
docker system df               # 종류별 용량과 회수 가능량
docker builder prune -a -f     # 빌드 캐시 전부 삭제 — 다음 빌드가 느려질 뿐 안전하다
```


# 3. Images — 개발 PC

- 빌드된 앱 이미지는 `myapp/api:<버전>` 처럼 버전 태그가 붙어 남는다.
- `docker save` 로 tar 를 만들면 이미지가 **Docker 밖 파일**로 복사된다. 이 파일은 `docker system df` 에 안 잡히니 디스크 용량을 따로 봐야 한다.
- 옛 버전 태그를 지워도 tar 가 남아 있으면 `docker load` 로 되살릴 수 있다.
- 인프라 이미지(DB·메시지 큐 등)는 빌드가 아니라 `pull` 이라 캐시는 안 쌓이고 이미지로만 남는다.

# 4. Volumes — 개발 PC 는 무관, 서버에서 일어나는 일


### 개발 PC

- `build`·`pull`·`save` 는 **컨테이너를 띄우지 않으므로 볼륨을 만들지 않는다.** PC 에 보이는 볼륨은 로컬에서 스택을 직접 띄웠을 때 생긴 것이다.

### 서버

1. 새 버전 디렉토리에 번들을 풀고 이전 설치의 `.env`·인증서 같은 설정을 가져온다.
2. `docker load` 로 이미지를 올린다.
3. `docker compose up -d` 를 실행하면:
    - compose 의 named volume 실제 이름은 **`<프로젝트명>_<볼륨명>`** 이다 (예: `myapp_postgres-data`).
    - 디렉토리가 바뀌어도 프로젝트명(`-p` 또는 `name:`)이 같으면 **같은 볼륨에 다시 붙는다** → DB 데이터 유지.
    - 볼륨이 없을 때(첫 설치)만 새로 만들어진다.
    - `down -v` 를 하지 않는 한 데이터는 사라지지 않는다.
4. 헬스체크가 실패해 이전 버전으로 롤백해도 **볼륨은 같은 것을 쓰므로 데이터는 되돌아가지 않는다.** 마이그레이션이 이미 적용됐다면 옛 버전이 새 스키마를 읽게 되는 점을 조심.

> ⚠️ 프로젝트명을 바꾸면(디렉토리 이름에 의존하는 기본값 포함) compose 는 **빈 새 볼륨**을 만들고, 옛 데이터 볼륨은 연결이 끊긴 채 남는다. "배포했더니 데이터가 사라졌다" 의 흔한 원인.


### 이름 없는(익명) 볼륨 찌꺼기

- Dockerfile 에 `VOLUME` 이 선언된 이미지(예: DB 이미지)를 compose 에서 named volume 으로 덮지 않고 띄우면 **해시 이름 볼륨**이 생긴다.
- 컨테이너를 지워도 익명 볼륨은 남는다 → 스택을 올렸다 내렸다 하면 수십~수백 MB 씩 쌓인다.
- `docker volume prune` 은 기본적으로 **연결 없는 익명 볼륨만** 지운다 (named volume 은 `-a` 를 줘야 지워짐).

# 5. 서버 쪽 이미지는 누가 지우나

- load 한 버전별 이미지는 보통 **아무도 안 지운다** → 배포할 때마다 쌓인다.
- 그런데 롤백하려면 이전 버전 이미지가 있어야 한다. 서버에서 `docker image prune -a` 를 무심코 돌리면 **실행 중이 아닌 이전 버전 이미지가 지워져 롤백이 막힌다.**
- 서버는 "현재 + 직전 N 버전은 남기고 나머지 태그만 `docker rmi`" 처럼 보존 규칙을 두고 지우는 게 안전하다.

# 6. 정리 명령 치트시트


| 명령                           | 지우는 것               | 주의                                        |
| ---------------------------- | ------------------- | ----------------------------------------- |
| `docker builder prune -a -f` | 빌드 캐시 전부            | 다음 빌드가 느려짐 (안전)                           |
| `docker volume prune -f`     | 연결 없는 익명 볼륨         | named volume 은 안 지움                       |
| `docker image prune -f`      | 태그 없는(dangling) 이미지 | 안전                                        |
| `docker image prune -a -f`   | 컨테이너가 안 쓰는 이미지 전부   | 멈춘 컨테이너가 쓰는 이미지는 남음. **서버에선 롤백 이미지까지 지움** |
| `docker container prune -f`  | 멈춘 컨테이너             | 그 컨테이너의 named volume 은 남음                 |
| `docker system df -v`        | (조회만)               | 볼륨별 LINKS 0 = 연결 없음                       |


> 💡 prune 해도 Docker Desktop(macOS)의 디스크 이미지 파일(`Docker.raw`)은 바로 줄지 않을 수 있다. Finder 용량이 그대로면 Docker Desktop 재시작.


# 7. 배운 것

1. **Build Cache 와 Images 는 다른 저장소다.** 멀티스테이지 빌드의 빌드 단계는 이미지 목록엔 없고 캐시에만 남는다.
2. **볼륨은 빌드와 무관하고 "컨테이너를 띄운 곳"에만 생긴다.** 데이터 유지 여부는 compose 프로젝트명과 `down -v` 여부로 결정된다.
3. **롤백은 이미지만 되돌린다.** 데이터(볼륨)는 그대로라 스키마 변경이 있는 배포는 롤백 계획을 따로 세워야 한다.
4. **개발 PC 는 prune 을 과감하게, 서버는 보존 규칙을 두고.**
