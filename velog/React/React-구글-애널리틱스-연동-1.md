---
title: "[React] 구글 애널리틱스 연동 -1"
tags: ["Google Analytics", "React", "방문자수"]
date: 2024-05-11
velog_id: 706033d8-194d-4ef4-aa03-70a4a561727d
velog_url: https://velog.io/@steela/React-구글-애널리틱스-연동-1
velog_updated: 2026-07-15T21:56:11.075Z
synced_at: 2026-07-16
---

> 🔗 원본: [velog.io/@steela/React-구글-애널리틱스-연동-1](https://velog.io/@steela/React-구글-애널리틱스-연동-1) · 📅 2024-05-11
## 구글 애널리틱스란?
> Google 애널리틱스는 웹사이트와 앱 실적을 심도 있게 파악하고자 하는 수백만 명의 웹사이트 및 앱 소유자가 이용하는 플랫폼입니다. Google 애널리틱스를 사용하면 디지털 전략을 세부적으로 조정하고, 캠페인을 최적화하며, 온라인 인지도를 한 단계 업그레이드할 수 있습니다.

사이트 방문자 수 집계를 위해 한 번 사용해보겠다!

<br />

## 구글 애널리틱스 연동

1. 먼저 구글 애널리틱스에 가입하여 계정을 만든다.
웹 스트림 작성까지 마치면 설치 안내 페이지가 나타난다.

2. 아래 코드를 복사한 후 웹사이트의 각 페이지 코드에서 <head> 요소 바로 다음에 붙여넣으면 된다. Google 태그는 각 페이지에 하나씩만 추가.

**React에서는 @public\index.html의 head 태그 바로 아래에 붙여넣으면 된다.**


  
```
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=본인태그"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-3G1QV2DWDS');
</script>
```
  
  3. 해당 코드를 삽입해주었다면, 빌드 후 배포환경에 적용하여 [테스트] 버튼을 눌러 코드가 정상적으로 삽입되었는지, GA에서 인식할 수 있는지 확인할 수 있다.
  
  <br />
  

  > 참고
  https://velog.io/@slight-snow/React.js-GA4Google-Analytics-4-%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0
