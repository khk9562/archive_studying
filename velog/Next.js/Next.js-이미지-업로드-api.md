---
title: "[Next.js] node.js 기반 이미지 업로드 api"
tags: ["arraybuffer", "crypto", "formData", "fs", "image upload api", "next.js", "node.js", "path"]
date: 2024-03-24
velog_id: 3e6a945c-7230-4123-add7-973887af67c0
velog_url: https://velog.io/@steela/Next.js-이미지-업로드-api
velog_updated: 2026-07-09T09:00:51.827Z
synced_at: 2026-07-09
---

> 🔗 원본: [velog.io/@steela/Next.js-이미지-업로드-api](https://velog.io/@steela/Next.js-이미지-업로드-api) · 📅 2024-03-24
이 api는 당시 프로젝트에서 추가적인 라이브러리 및 서버 사용을 지양하고 싶어 구현하였다. 먼저 app 폴더 안에 api 폴더를 생성한 후, upload 폴더안에 route.ts 파일 안에 작성하였다.

```
import fs from "fs";
import { NextResponse } from "next/server";
import crypto from "crypto";
import path from "path";

export async function POST(req: Request) {
  const formData = await req.formData();
  const formDataEntryValues = Array.from(formData.values());

  let fileUrls = [];
  const allowedExtensions = /(\.jpg|\.jpeg|\.png|\.gif|\.svg)$/i;
  for (const formDataEntryValue of formDataEntryValues) {
    if (
      typeof formDataEntryValue === "object" &&
      "arrayBuffer" in formDataEntryValue
    ) {
      const file = formDataEntryValue as unknown as Blob;
      const buffer = Buffer.from(await file.arrayBuffer());

      if (!allowedExtensions.exec(file.name)) {
        return NextResponse.json({
          success: false,
          message: "업로드할 수 있는 확장자: .jpg, .jpeg, .png, .gif, .svg",
        });
      }

      const randomFileName =
        crypto.randomBytes(15).toString("hex") + path.extname(file.name);
      const filePath = `public/upload/${randomFileName}`;

      fs.writeFileSync(filePath, buffer);
      // fileUrls.push(`http://localhost:3000/upload/${file.name}`);
      fileUrls.push(`/upload/${randomFileName}`);
    }
  }
  return NextResponse.json({ success: true, urls: fileUrls });
}

```
**fs**모듈은 파일에 대한 연산을 지원하는 모듈이다. fs.writeFileSync()는 fs파일에 동기적으로 데이터를 쓰는 데 사용되며 파일이 없으면 파일을 만들고 파일이 있는 경우 덮어쓴다.
**crypto**는 다양한 방식의 암호화를 지원하는 모듈이다. 단방향 암호화 (해시) 및 양방향 대칭/비대칭 암호화 모두 지원한다. 이를 이용하여 같은 파일 이름의 업로드를 막기 위해 파일 이름을 랜덤화 처리하였다.
**buffer**는 메모리 상에 작업할 내용 전부를 저장해둔 뒤 일괄 처리하고, 스트림은 작업할 내용을 작은 단위로 모아서 짧게 짧게 처리하는 방식이다.
**path**를 사용하여 포트 넘버가 바뀔 경우를 대비해 절대 경로로 수정하였다.
이상한 파일 업로드를 막고 이미지만 허용하기 위해 확장자를 이미지 파일로만 제한하였다.


이 api를 실제로 사용한 코드도 추가하겠다.

```
  const handleFileSelected = async (e: React.ChangeEvent<HTMLInputElement>) => {
    if (e.target.files) {
      const files = Array.from(e.target.files);
      const allowedExtensions = /(\.jpg|\.jpeg|\.png|\.gif|\.svg)$/i;
      const formData = new FormData();

      const newFilesUrls = files.map((file) => URL.createObjectURL(file));

      files.forEach((file, i) => {
        if (!allowedExtensions.exec(files[i].name)) {
          // alert("이미지 파일(.jpg, .jpeg, .png, .gif, .svg)만 업로드해주세요.");
          e.target.value = "";
          setAlertImgAllowed(true);
          return;
        }
        formData.append(file.name, file);
      });
      try {
        const response = await axios.post("/api/upload", formData);

        if (response.data.success) {
          setImagesUrls((prevImagesUrls) => [
            ...prevImagesUrls,
            ...response.data.urls,
          ]);
        } else {
          console.log("업로드 실패:", response.data.message);
        }
      } catch (e) {
        console.error("handleFileSelected", e);
      }
    }
  };
```

여전히 각각의 개념에 대한 이해가 얕다. 더 공부하자.


> **출처**
[fs, crypto 모듈](https://ngwoon.github.io/node/2021/02/14/Node-%EC%9E%90%EC%A3%BC-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-%EB%AA%A8%EB%93%882/)
[fs.writeFileSync()](https://velog.io/@maxminos/fs.writeFileSync)
[crypto.randomBytes()](https://velog.io/@kaitlin_k/%EC%95%94%ED%98%B8%ED%99%94-%EB%B0%A9%EC%8B%9D)
[arrayBuffer](https://velog.io/@dusunax/File-Blob-ArrayBuffer-TypedArrayUint8Array)
