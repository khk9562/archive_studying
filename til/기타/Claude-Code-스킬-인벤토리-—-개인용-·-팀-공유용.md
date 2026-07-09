---
title: "Claude Code 스킬 인벤토리 — 개인용 · 팀 공유용"
tags: ["AI"]
date: 2026-07-09
notion_id: 398922cf-26a8-818b-b6ef-cc5fb8c9db29
notion_last_edited: 2026-07-09T04:48:00.000Z
synced_at: 2026-07-09
---
> 📅 **학습일**: 2026-07-09

> **기준일** 2026-07-09 · **환경** `~/.claude` + `v3` 레포

## 개요

- **Claude Code 스킬**이란: `SKILL.md`(YAML frontmatter의 `name` + `description`)로 정의되는 확장 기능. 설명에 적힌 트리거 상황이 오면 Claude가 자동 호출하거나 `/스킬명`으로 수동 호출한다.
- **설치 위치 2종**
    - **개인용** `~/.claude/skills/` : 내 모든 프로젝트에서 공통으로 쓰임 (레포에 안 올라감)
    - **팀 공유용** `프로젝트/.claude/skills/` : 레포에 커밋되어 팀원과 공유됨
- **커스텀 여부**
    - 🛠 **커스텀** = 내가 직접 작성한 스킬 (한국어·이 프로젝트 관행 반영)
    - 📦 **외부** = 마켓플레이스/GitHub에서 설치한 스킬 (출처 명시)

> 📊 **요약** — 개인용 11개 (커스텀 5 · 외부 6) · 팀 공유 3개 (커스텀 2 · 외부 1)


---


## 1. 개인용 스킬 — `~/.claude/skills/`


### 1-1. 🛠 직접 커스텀 제작 (5)


| 스킬                 | 담고 있는 내용                                                                                                                                            | 트리거                                                  |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **create-mock**    | React 컴포넌트의 데이터 리스트 변수를 N개로 복제하는 임시 미리보기 코드를 삽입. 항상 `⚠️ TEMP` 마커를 넣어 delete-mock으로 안전 원복 가능.                                                        | "mock 데이터 추가", "미리보기 N개로 부풀려줘", `/create-mock`       |
| **delete-mock**    | create-mock이 넣은 `⚠️ TEMP` 코드를 grep으로 정확히 찾아 useMemo 블록·raw 변수·map의 key/idx 패턴까지 원래대로 복구.                                                            | "mock 원복", "미리보기 데이터 지워줘", `/delete-mock`            |
| **dataviz-design** | Canvas·D3·ECharts·SVG 데이터 시각화의 (A)신규 설계 + (B)기존 위젯/차트 개선. 차트 유형·렌더 기술 선택, 색·모션·접근성·반응형·성능·입체감 품질 기준 제시. 개선 모드는 기존 동작 회귀 0으로 최소 편집.                  | "차트/히트맵/토폴로지 만들어줘", "이 위젯 디자인 개선", `/dataviz-design` |
| **find-motion**    | 한국어로 묘사한 웹 애니메이션/모션을 영문 기법명으로 매핑해 CodePen·Codrops·Shadertoy에서 레퍼런스 검색·핵심 테크닉 해설.                                                                    | "이런 애니메이션 찾아줘", "일렁이는 효과 레퍼런스", `/find-motion`       |
| **fix-bug**        | 버그를 패턴매칭 대신 실제 코드 경로 재현/추적 → 근본 원인 명시 → 사용자 확인 후 최소 수정 → build+lint → 커밋 메시지 제안. _본문에 내 커밋 컨벤션(대문자 prefix, no Co-Authored-By)이 그대로 박혀 있어 커스텀으로 분류._ | 버그 수정 요청 시                                           |


### 1-2. 📦 외부 설치 (6)


| 스킬                          | 출처                                                                             | 담고 있는 내용                                                                                                                  |
| --------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| **archify** (2026-07-09 설치) | [tt-a1i/archify](https://github.com/tt-a1i/archify) · MIT v2.10 · Cocoon-AI 기반 | 아키텍처·워크플로우·시퀀스·데이터플로우·라이프사이클 다이어그램을 **자체 완결형 HTML(SVG)** 로 생성. 다크/라이트 토글, PNG/JPEG/WebP/SVG 내보내기. 평문 설명 또는 Mermaid 코드 입력. |
| **find-skills**             | [vercel-labs/skills](https://github.com/vercel-labs/skills)                    | 필요한 기능에 맞는 설치형 스킬을 탐색하고 설치를 안내.                                                                                           |
| **frontend-design**         | [anthropics/skills](https://github.com/anthropics/skills)                      | 템플릿 티 안 나는 개성 있는 UI 시각 디자인 방향·타이포·의도적 선택 가이드.                                                                             |
| **grill-me**                | [mattpocock/skills](https://github.com/mattpocock/skills)                      | 계획/설계를 집요하게 인터뷰해 다듬음 (모델 자동 호출 off — 수동 전용).                                                                              |
| **grill-with-docs**         | [mattpocock/skills](https://github.com/mattpocock/skills)                      | grill-me + 진행하며 ADR·용어집 문서를 함께 생성.                                                                                        |
| **grilling**                | [mattpocock/skills](https://github.com/mattpocock/skills)                      | 빌드 전 계획을 집요하게 스트레스 테스트. 'grill' 트리거 문구로 호출.                                                                               |


---


## 2. 팀 공유용 스킬 — `sns-프로젝트S-v3/.claude/skills/`


### 2-1. 🛠 직접 커스텀 제작 (2)


| 스킬                   | 담고 있는 내용                                                           | 트리거                                             |
| -------------------- | ------------------------------------------------------------------ | ----------------------------------------------- |
| **create-storybook** | `shared/ui` 컴포넌트의 Storybook stories(CSF 3) 파일을 프로젝트 컨벤션에 맞춰 생성.    | "스토리북 만들어줘", `/create-storybook 컴포넌트명`          |
| **update-storybook** | 기존 `shared/ui` 컴포넌트의 props/variant가 바뀌었을 때 해당 `.stories.tsx`를 동기화. | "이 컴포넌트 stories도 갱신", `/update-storybook 컴포넌트명` |


### 2-2. 📦 외부 설치 (1)


| 스킬                | 출처                            | 담고 있는 내용                                               |
| ----------------- | ----------------------------- | ------------------------------------------------------ |
| **accessibility** | web-quality-skills · MIT v1.1 | WCAG 2.2 기준 웹 접근성 감사·개선. 스크린리더 지원, 키보드 내비게이션, a11y 감사. |


---


## 참고

- 개인용 중 `find-skills`·`frontend-design`·`grill-me`·`grill-with-docs`·`grilling`은 `~/.agents/skills/`에 설치된 실체를 심볼릭 링크로 참조하며 `.skill-lock.json`이 출처를 관리한다.
- archify는 스키마 검증용 `ajv` 의존성 때문에 설치 후 `npm install`을 1회 수행했다.
