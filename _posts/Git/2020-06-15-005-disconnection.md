---
layout: post
title: 로컬 저장소 및 원격 저장소 연결 해제
categories: Git
tags: [Git]
---

1\. 명령어를 통한 해제

```bash
$ git remote remove origin

$ git remote -v
```

로컬 저장소 : O

원격 저장소 : O

로컬 저장소와 원격 저장소 연결 상태 : X

2\. \[.git\] 파일 수동 삭제

로컬 저장소 폴더로 직접 가서 .git 파일들 모두 삭제

로컬 저장소 : X

원격 저장소 : O

로컬 저장소와 원격 저장소 연결 상태 : X