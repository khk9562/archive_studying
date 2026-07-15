---
title: "[React] Table에서 tbody만 스크롤"
tags: ["CSS", "Table", "Thead", "sticky", "tbody scroll"]
date: 2024-03-05
velog_id: 0584d260-22fd-4359-8b81-7b3b03b0dbfd
velog_url: https://velog.io/@steela/Table-thead-고정-tbody만-스크롤
velog_updated: 2026-07-15T11:14:17.917Z
synced_at: 2026-07-15
---

> 🔗 원본: [velog.io/@steela/Table-thead-고정-tbody만-스크롤](https://velog.io/@steela/Table-thead-고정-tbody만-스크롤) · 📅 2024-03-05
Table에 직접적으로 스크롤을 걸면 thead도 함께 스크롤된다.

thead가 항상 보이되, 이걸 CSS로 구현할 수 있는지 궁금하여 찾아보다 position: sticky;를 유용하게 사용하였다.

```
<div className={styles.scrollable}>
  <Table hover striped>
    <thead
      style={{
        backgroundColor: "#ddd",
        textAlign: "center",
        position: "sticky",
        top: "0",
      }}
    >
      <tr></tr>
    </thead>
    <tbody>
      <tr></tr>
    </tbody>
  </Table>
</div>
```

```
.scrollable {
  max-height: calc(50vh - 70px);
  overflow-y: scroll;
}
```

추가로 이미 Table을 Bootstrap으로 사용하고 있었기 때문에,

BootStrap 라이브러리를 잘 살펴보면 커뮤니티에서 추가로 제공되는 기능으로 구현할 수 있다.

하지만 나는 추가로 라이브러리를 설치하고 싶지 않아 직접 구현했다.
