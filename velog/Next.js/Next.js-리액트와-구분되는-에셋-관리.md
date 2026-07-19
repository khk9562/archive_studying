---
title: "[Next.js] 리액트와 구분되는 에셋 관리"
tags: ["Assets", "next.js", "react.js"]
date: 2025-09-16
velog_id: 7e1a2d87-cb51-40b3-93db-d134410d276a
velog_url: https://velog.io/@steela/Next.js-리액트와-구분되는-에셋-관리
velog_updated: 2026-07-19T11:25:27.938Z
synced_at: 2026-07-19
---

> 🔗 원본: [velog.io/@steela/Next.js-리액트와-구분되는-에셋-관리](https://velog.io/@steela/Next.js-리액트와-구분되는-에셋-관리) · 📅 2025-09-16
⏺ Next.js에서 폰트 파일 위치별 장단점 비교

  📁 public/static/fonts/ (정적 파일)

  장점:
  - 직접 URL 접근 가능 (/static/fonts/font.ttf)
  - CSS에서 간단한 참조 (url('/static/fonts/font.ttf'))
  - 번들 크기 감소 (JS 번들에 포함되지 않음)
  - 서버 캐싱 최적화 (CDN, 브라우저 캐시 활용)
  - SSR 호환성 우수

  단점:
  - 빌드 최적화 없음 (압축, 해시 등)
  - import 불가 (TypeScript 타입 지원 없음)
  - 동적 로딩 불가
  - Webpack 최적화 혜택 없음

  ---
  📁 src/assets/fonts/ (번들링)

  장점:
  - Webpack 최적화 (압축, 해시된 파일명)
  - import로 타입 안전성 확보
  - 동적 import 가능
  - Tree shaking 적용
  - 모듈 시스템 활용

  단점:
  - 번들 크기 증가 (특히 큰 폰트 파일)
  - 초기 로딩 시간 증가 가능
  - CSS에서 직접 참조 어려움
  - 복잡한 설정 필요할 수 있음

  ---
  📋 권장 사용법

  큰 폰트 파일 (여러 개): public/static/fonts/작은 아이콘 폰트: src/assets/fonts/
