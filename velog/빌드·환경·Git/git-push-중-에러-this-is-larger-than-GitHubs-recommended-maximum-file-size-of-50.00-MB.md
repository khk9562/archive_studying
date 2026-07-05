---
title: "git push 중 에러: this is larger than GitHub's recommended maximum file size of 50.00 MB"
tags: ["git", "github"]
date: 2024-03-26
velog_id: c67d466a-52a0-480c-9200-74eb2d205c77
velog_url: https://velog.io/@steela/git-push-중-에러-this-is-larger-than-GitHubs-recommended-maximum-file-size-of-50.00-MB
velog_updated: 2026-07-04T16:42:31.066Z
synced_at: 2026-07-05
---

> 🔗 원본: [velog.io/@steela/git-push-중-에러-this-is-larger-than-GitHubs-recommended-maximum-file-size-of-50.00-MB](https://velog.io/@steela/git-push-중-에러-this-is-larger-than-GitHubs-recommended-maximum-file-size-of-50.00-MB) · 📅 2024-03-26
GitHub에서 새로운 레포지토리를 파서 기존에 있던 코드를 부분추출하여 push 하였다. 그 과정에서 webpack안의 캐시의 용량이 github에서 권장하는 파일 크기를 초과하여 자꾸 push가 되지 않고 Git LFS(Large File Storage)를 받으라는 경고가 출력되었다.

하지만 따로 설치하고 싶지 않았고, 개인당 2GB가 넘어가면 유료 결제를 해야하기 때문에 어떻게든 기존 설치 환경을 유지하고 싶었다.

그래서 찾아보던 중 [이 페이지](https://github.com/vercel/next.js/discussions/29233)에 남겨진 댓글로 겨우 push에 성공하여 기록에 남긴다. 같은 오류를 겪고 있다면 **주의하여** 아래 명령어들을 따라해주세요..!
<br />
#### 첫번째

```
git reset --hard HEAD
```
이 명령은 현재 작업 디렉토리를 이전 커밋(가장 최신의 커밋, HEAD)으로 되돌린다. --hard 옵션은 작업 디렉토리의 변경 사항을 모두 삭제하고 이전 커밋으로 되돌리는 것이다.
<br />
#### 두번째

```
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch '.next/cache/webpack/client-development/0.pack'" --prune-empty --tag-name-filter cat -- --all
```
**.next/cache/webpack/client-development/0.pack**
이 부분은 경고에 출력되는 용량 초과 파일 경로를 그대로 입력해주면 된다.

이 명령은 과거 커밋에서 특정 파일을 삭제하는 작업을 수행한다. --index-filter 옵션은 각 커밋에서 지정된 파일을 삭제하는데 사용된다. --prune-empty는 빈 커밋을 제거한다. --tag-name-filter cat은 태그를 필터링하는데 사용된다. -- --all은 모든 브랜치에 대해 이 작업을 수행하도록 지정한다.
<br />
#### 마지막

```
git push --force
```
이 명령은 로컬 변경 사항을 원격 저장소에 강제로 푸시한다. --force 옵션은 기존의 히스토리를 덮어쓰기 때문에 주의!

<br />
<br />

**레포지토리를 처음 세팅한 것이고 개인 작업이기 때문에 강제로 시행한 명령이 많은데, git 명령어에 대한 추가적인 공부가 많이 필요할 것 같다...!**
