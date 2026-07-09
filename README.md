# 📚 archive_studying

> 🌐 **웹사이트**: https://khk9562.github.io/archive_studying/ (폴더별로 게시글처럼 보기)
>
> 학습 기록 아카이브 — **Notion TIL**과 **Velog** 글을 자동으로 마크다운 동기화합니다.
> 이 파일은 자동 생성됩니다. 직접 수정하지 마세요 — 동기화 시 덮어쓰여집니다.
> 작성은 Notion/Velog에서, 공개 글만 흡수되며 회사·보안 정보는 필터링됩니다.


## 📖 이 레포는?

Notion의 `🌱 TIL` 데이터베이스를 **GitHub로 단방향 자동 동기화**하는 미러입니다. 작성은 Notion에서 하고, 공개해도 되는 글만 골라 마크다운으로 흡수합니다.

### 동작 방식
- **매일 한국시간 오후 8시(20:00)** GitHub Actions가 Notion을 읽어 마크다운으로 변환 후 커밋합니다. (수동 실행도 가능)
- Notion에서 `공개` 체크박스를 켠 글만 대상이 됩니다.
- **증분 동기화** — Notion에서 마지막으로 편집한 글만 덮어쓰고, 안 바뀐 글은 그대로 둡니다. 변경이 없는 날은 커밋도 생기지 않습니다.
- 공개를 끄거나 삭제한 글은 레포에서도 제거됩니다. (치환사전/denylist를 바꾼 뒤 전 글에 재적용하려면 *force* 재빌드 사용)
- 각 글에는 학습 날짜(또는 기간)가 표기되고, Notion의 **`폴더` 속성값**으로 폴더가 정해집니다(없으면 첫 태그 → 기타). 새 분류가 필요하면 Notion에서 `폴더` 옵션을 새로 만들면 그게 곧 새 폴더입니다. 아래 인덱스도 **폴더별**로 자동 갱신됩니다.

### 보안 필터 (공개 레포 안전장치)
- **치환 사전** — 회사명·프로젝트명·벤더명 등 민감어를 대체어로 치환. 매핑은 `REDACTION_MAP` GitHub Secret에만 두고 레포엔 커밋하지 않습니다.
- **자동 마스킹** — 이메일은 `<email>`, IP 주소는 `<ip주소>` 로 가린 뒤 발행합니다.
- **위험 차단** — AWS 키(AKIA/ASIA)·presigned URL·각종 토큰/JWT·시크릿 할당이 남아 있으면 **그 글은 발행 차단 + 로그**.
- **이미지 자동 제외** — 노션 업로드 이미지는 스크린샷 유출·presigned URL(임시 AWS 토큰) 위험이 있어 본문에서 제거합니다.

### 알림
- Telegram으로 **🔄 시작 / ✅ 완료(발행·차단 목록 포함) / ❌ 실패** 알림을 보냅니다. (봇 시크릿 미설정 시 자동 비활성화)

> 📦 Velog 글은 [`velog/`](velog/README.md) 폴더에 같은 분류 체계로 별도 아카이빙됩니다.

---

# 📒 Notion TIL

총 **57**개의 글 · **12**개 폴더 · [📦 Velog 아카이브 →](velog/README.md)

## React (11)

- 2026-06-24 · [useSyncExternalStore — 외부 store(matchMedia)를 안전하게 구독하는 React 훅](til/React/useSyncExternalStore-%E2%80%94-%EC%99%B8%EB%B6%80-store(matchMedia)%EB%A5%BC-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EA%B5%AC%EB%8F%85%ED%95%98%EB%8A%94-React-%ED%9B%85.md) _(React, Typescript)_
- 2026-05-20 · [컴포넌트 미분리 및 State 위치에 따른 렌더링 성능 개선](til/React/%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EB%AF%B8%EB%B6%84%EB%A6%AC-%EB%B0%8F-State-%EC%9C%84%EC%B9%98%EC%97%90-%EB%94%B0%EB%A5%B8-%EB%A0%8C%EB%8D%94%EB%A7%81-%EC%84%B1%EB%8A%A5-%EA%B0%9C%EC%84%A0.md) _(React)_
- 2025-11-28 · [리액트 명령형 VS 함수형 문법](til/React/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%AA%85%EB%A0%B9%ED%98%95-VS-%ED%95%A8%EC%88%98%ED%98%95-%EB%AC%B8%EB%B2%95.md)
- 2025-10-13 · [React Dev Tools (Chrome Extensions)](til/React/React-Dev-Tools-(Chrome-Extensions).md) _(React, Extension)_
- 2024-08-21 · [리액트 최적화](til/React/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%B5%9C%EC%A0%81%ED%99%94.md)
- 2024-07-05 · [[React] react-hook-form](til/React/%5BReact%5D-react-hook-form.md)
- 2024-06-18 · [[React] App.css VS index.css](til/React/%5BReact%5D-App.css-VS-index.css.md) _(React)_
- 2024-06-17 · [리액트 라이브러리](til/React/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC.md) _(React)_
- 2024-06-11 · [[React] Axios로 API 통신](til/React/%5BReact%5D-Axios%EB%A1%9C-API-%ED%86%B5%EC%8B%A0.md) _(React)_
- 2024-06-11 · [[React]propTypes](til/React/%5BReact%5DpropTypes.md) _(React)_
- 2024-06-10 · [생활코딩! React 리액트 프로그래밍](til/React/%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9!-React-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D.md) _(React)_

## 백엔드·인프라 (7)

- 2025-05-08 · [로그인 및 보안](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EB%B0%8F-%EB%B3%B4%EC%95%88.md)
- 2025-02-20 · [Web Socket](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/Web-Socket.md)
- 2024-12-10 · [백엔드](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/%EB%B0%B1%EC%97%94%EB%93%9C.md)
- 2024-06-14 · [Nginx](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/Nginx.md)
- 2024-06-14 · [Nginx + proxy + origin + CORS](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/Nginx-+-proxy-+-origin-+-CORS.md)
- 2024-06-11 · [[Udemy] FastAPI101](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/%5BUdemy%5D-FastAPI101.md) _(FastAPI)_
- 2024-06-11 · [FastAPI Deep Dive](til/%EB%B0%B1%EC%97%94%EB%93%9C%C2%B7%EC%9D%B8%ED%94%84%EB%9D%BC/FastAPI-Deep-Dive.md) _(FastAPI)_

## 빌드·환경·Git (7)

- 2026-06-26 · [Jenkins + Docker + Nexus 기반 프론트엔드 CI/CD 배포 파이프라인 (이미지 레지스트리 경유 패턴)](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/Jenkins-+-Docker-+-Nexus-%EA%B8%B0%EB%B0%98-%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-CICD-%EB%B0%B0%ED%8F%AC-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-(%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC-%EA%B2%BD%EC%9C%A0-%ED%8C%A8%ED%84%B4).md) _(architecture, CS, Vite)_
- 2025-11-16 · [Git 협업 전략](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/Git-%ED%98%91%EC%97%85-%EC%A0%84%EB%9E%B5.md)
- 2025-02-03 · [git branch 생성](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/git-branch-%EC%83%9D%EC%84%B1.md) _(Git)_
- 2024-06-26 · [모노레포 세팅](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/%EB%AA%A8%EB%85%B8%EB%A0%88%ED%8F%AC-%EC%84%B8%ED%8C%85.md) _(React, Vite, Turbo, SCSS, CoreUI)_
- 2024-06-11 · [[React + vite] 폴더구조로 route 구성하기](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/%5BReact-+-vite%5D-%ED%8F%B4%EB%8D%94%EA%B5%AC%EC%A1%B0%EB%A1%9C-route-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0.md) _(React, Vite)_
- 2024-06-10 · [Vite](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/Vite.md) _(Vite)_
- 2024-06-04 · [[React + Vite / Fast api] 환경세팅](til/%EB%B9%8C%EB%93%9C%C2%B7%ED%99%98%EA%B2%BD%C2%B7Git/%5BReact-+-Vite-Fast-api%5D-%ED%99%98%EA%B2%BD%EC%84%B8%ED%8C%85.md) _(React, Vite, FastAPI)_

## JavaScript·TypeScript (7)

- 2025-02-24 · [비동기/동기](til/JavaScript%C2%B7TypeScript/%EB%B9%84%EB%8F%99%EA%B8%B0%EB%8F%99%EA%B8%B0.md)
- 2024-07-16 · [함수 재사용](til/JavaScript%C2%B7TypeScript/%ED%95%A8%EC%88%98-%EC%9E%AC%EC%82%AC%EC%9A%A9.md)
- 2023-05-03 · [[Javascript] 코딩앙마중급 #15 클래스](til/JavaScript%C2%B7TypeScript/%5BJavascript%5D-%EC%BD%94%EB%94%A9%EC%95%99%EB%A7%88%EC%A4%91%EA%B8%89-15-%ED%81%B4%EB%9E%98%EC%8A%A4.md) _(Javascript)_
- 2023-05-03 · [[Javascript] 코딩앙마 중급#13 call, apply, bind](til/JavaScript%C2%B7TypeScript/%5BJavascript%5D-%EC%BD%94%EB%94%A9%EC%95%99%EB%A7%88-%EC%A4%91%EA%B8%8913-call,-apply,-bind.md) _(Javascript)_
- 2023-05-03 · [[Typescript] 코딩앙마 강의 기록](til/JavaScript%C2%B7TypeScript/%5BTypescript%5D-%EC%BD%94%EB%94%A9%EC%95%99%EB%A7%88-%EA%B0%95%EC%9D%98-%EA%B8%B0%EB%A1%9D.md) _(Typescript)_
- 2023-04-22 · [[Javascript] 드림코딩 | 배열 관련 함수](til/JavaScript%C2%B7TypeScript/%5BJavascript%5D-%EB%93%9C%EB%A6%BC%EC%BD%94%EB%94%A9-%EB%B0%B0%EC%97%B4-%EA%B4%80%EB%A0%A8-%ED%95%A8%EC%88%98.md) _(Javascript)_
- 2023-04-22 · [[Javascript] 드림코딩 | 핵심 차세대 자바스크립트 기능](til/JavaScript%C2%B7TypeScript/%5BJavascript%5D-%EB%93%9C%EB%A6%BC%EC%BD%94%EB%94%A9-%ED%95%B5%EC%8B%AC-%EC%B0%A8%EC%84%B8%EB%8C%80-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EA%B8%B0%EB%8A%A5.md) _(Javascript)_

## 데이터·상태관리 (5)

- 2025-04-07 · [tanstack query 호출 후 onSuccess 인자](til/%EB%8D%B0%EC%9D%B4%ED%84%B0%C2%B7%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC/tanstack-query-%ED%98%B8%EC%B6%9C-%ED%9B%84-onSuccess-%EC%9D%B8%EC%9E%90.md) _(TanstackQuery)_
- 2025-03-11 · [tanstak-query 적용](til/%EB%8D%B0%EC%9D%B4%ED%84%B0%C2%B7%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC/tanstak-query-%EC%A0%81%EC%9A%A9.md)
- 2025-03-04 · [tanstack-query](til/%EB%8D%B0%EC%9D%B4%ED%84%B0%C2%B7%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC/tanstack-query.md)
- 2024-08-20 · [[fetch] Promise all](til/%EB%8D%B0%EC%9D%B4%ED%84%B0%C2%B7%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC/%5Bfetch%5D-Promise-all.md)
- 2024-07-16 · [axios](til/%EB%8D%B0%EC%9D%B4%ED%84%B0%C2%B7%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC/axios.md)

## 기타 (4)

- 2026-07-09 · [Claude Code 스킬 인벤토리 — 개인용 · 팀 공유용](til/%EA%B8%B0%ED%83%80/Claude-Code-%EC%8A%A4%ED%82%AC-%EC%9D%B8%EB%B2%A4%ED%86%A0%EB%A6%AC-%E2%80%94-%EA%B0%9C%EC%9D%B8%EC%9A%A9-%C2%B7-%ED%8C%80-%EA%B3%B5%EC%9C%A0%EC%9A%A9.md) _(AI)_
- 2026-06-22 · [채용 공고 크롤러 & 텔레그램 알림 봇 구축](til/%EA%B8%B0%ED%83%80/%EC%B1%84%EC%9A%A9-%EA%B3%B5%EA%B3%A0-%ED%81%AC%EB%A1%A4%EB%9F%AC-&-%ED%85%94%EB%A0%88%EA%B7%B8%EB%9E%A8-%EC%95%8C%EB%A6%BC-%EB%B4%87-%EA%B5%AC%EC%B6%95.md) _(Javascript)_
- 2026-05-06 · [하네스 엔지니어링](til/%EA%B8%B0%ED%83%80/%ED%95%98%EB%84%A4%EC%8A%A4-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81.md) _(AI)_
- 2025-10-13 · [VPN 이란?](til/%EA%B8%B0%ED%83%80/VPN-%EC%9D%B4%EB%9E%80.md) _(CS)_

## CSS·UI (4)

- 2025-11-16 · [UXUI 디자인 최적화 처리](til/CSS%C2%B7UI/UXUI-%EB%94%94%EC%9E%90%EC%9D%B8-%EC%B5%9C%EC%A0%81%ED%99%94-%EC%B2%98%EB%A6%AC.md)
- 2025-09-20 · [D3.js RoadMap](til/CSS%C2%B7UI/D3.js-RoadMap.md) _(d3.js)_
- 2024-07-08 · [[CSS] 스크롤 안보이게](til/CSS%C2%B7UI/%5BCSS%5D-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EC%95%88%EB%B3%B4%EC%9D%B4%EA%B2%8C.md) _(CSS)_
- 2024-06-24 · [[CSS] image loading: lazy vs eager](til/CSS%C2%B7UI/%5BCSS%5D-image-loading-lazy-vs-eager.md) _(CSS)_

## 아키텍처 (3)

- 2025-11-16 · [FSD 아키텍처](til/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/FSD-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.md) _(fsd, architecture)_
- 2025-10-19 · [프론트엔드 아키텍처](til/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.md)
- 2025-01-16 · [PageLayout의 페이지 header, nav 컨트롤 방법](til/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/PageLayout%EC%9D%98-%ED%8E%98%EC%9D%B4%EC%A7%80-header,-nav-%EC%BB%A8%ED%8A%B8%EB%A1%A4-%EB%B0%A9%EB%B2%95.md)

## Next.js (3)

- 2025-09-17 · [Next.js Before Migrating Company](til/Next.js/Next.js-Before-Migrating-Company.md) _(Next.js)_
- 2023-11-20 · [Learning Next js 14 (23-11-20)](til/Next.js/Learning-Next-js-14-(23-11-20).md) _(Next.js)_
- 2023-07-05 · [[Next.js] 세팅](til/Next.js/%5BNext.js%5D-%EC%84%B8%ED%8C%85.md) _(Next.js)_

## 결제 (2)

- 2024-08-20 · [[fetch] Promise - 포트원 결제 관련](til/%EA%B2%B0%EC%A0%9C/%5Bfetch%5D-Promise-%ED%8F%AC%ED%8A%B8%EC%9B%90-%EA%B2%B0%EC%A0%9C-%EA%B4%80%EB%A0%A8.md)
- 2024-07-12 · [포트원 결제 연동](til/%EA%B2%B0%EC%A0%9C/%ED%8F%AC%ED%8A%B8%EC%9B%90-%EA%B2%B0%EC%A0%9C-%EC%97%B0%EB%8F%99.md)

## 자격증 (2)

- 2024-10-16 · [SQLD](til/%EC%9E%90%EA%B2%A9%EC%A6%9D/SQLD.md) _(SQL)_
- 2024-03-05 · [정보처리기사 실기](til/%EC%9E%90%EA%B2%A9%EC%A6%9D/%EC%A0%95%EB%B3%B4%EC%B2%98%EB%A6%AC%EA%B8%B0%EC%82%AC-%EC%8B%A4%EA%B8%B0.md) _(정처기)_

## 회사 프로젝트 (2)

- 2026-06-08 · [프로젝트S v3](til/%ED%9A%8C%EC%82%AC%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8S-v3.md) _(React, Typescript, Vite, SCSS, TanstackQuery, d3.js, architecture)_
- 2026-03-03 · [프로젝트S v2](til/%ED%9A%8C%EC%82%AC%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8S-v2.md) _(Next.js, React, Typescript, SCSS, d3.js, TanstackQuery, architecture)_


---

## 🛠️ 로컬에서 실행하기

> 최초 설정(시크릿 등록·Notion 연결 등) 자세한 내용은 [SETUP.md](./SETUP.md) 참고.

```bash
npm install                 # 의존성 설치 (최초 1회)
cp .env.example .env        # 로컬 환경변수 준비 (최초 1회) → .env 에 토큰/치환사전 채우기

npm run sync:dry            # 테스트 동기화: 무엇이 발행/차단될지만 출력 (파일·커밋 없음)

npm run telegram:test               # 텔레그램 봇 연동 테스트
npm run telegram:test -- "메시지"    # 임의 메시지 전송

# 치환 사전(REDACTION_MAP)을 터미널에서 GitHub 시크릿으로 업로드 (GUI 타이핑 불필요)
cp secrets/redaction.example.json secrets/redaction.json   # 최초 1회, 편집
npm run secrets:redaction
```

- **수동 동기화**: GitHub Actions 탭 → *Sync TIL from Notion* → *Run workflow* (기본은 커밋 없는 테스트 실행, 실제 반영하려면 체크 해제).
- **자동 동기화**: 매일 한국시간 오후 8시(20:00).
