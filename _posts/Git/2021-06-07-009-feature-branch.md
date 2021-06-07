---
layout: post
title: Feature-Branch 전략
categories: Git
tags: [Git]
---

Feature-Branch 전략은 기능브랜치 전략이라고 한다.  
Git-Flow 전략의 간소화 된 버전으로 Master, Develop, Feature 3가지 Branch만 사용한다.

![Untitled](https://user-images.githubusercontent.com/48157259/120971023-263e1200-c7a7-11eb-854e-899e1329fc76.png)


### Branch 종류
1. Master Branch  
   - 배포 브랜치, 운영서버
   - 직접적인 PUSH 절대 불가

2. Develop Branch  
   - 개발병합 브랜치 : 기능이 개발된 브랜치를 병합하여 배포하기 위한 브랜치
   - Master Branch에 Merge 시켜 배포가 이루어짐
   - 브랜치 나오는 곳 : Master
   - 브랜치 들어가는 곳 : Master

3. Feature Branch (branch명 : feature/기능명)
   - 기능개발 브랜치
   - 개발자 로컬 저장소에서 관리, 개발자가 새로운 기능을 구현하는 브랜치
   - 구현된 각각의 기능들은 각 Feature Branch의 이름으로 Remote로 Push하여 PR함
   - PR된 Remote Feature 브랜치는 자동 삭제됨
   - 브랜치 나오는 곳 : Develop
   - 브랜치 들어가는 곳 : Develop


#### (1) Feature Branch 생성 후 Remote로 push
```bash
# feature 브랜치 생성(develop 브랜치를 따서 생성)
$ git checkout -b feature/menu develop

[developing....]

$ git add .
$ git commit -m "add menu"

# feature 브랜치 remote push
$ git push --set-upstream origin feature/menu
```

#### (2) Pull Request 하기
![_2021-06-07__1 33 29](https://user-images.githubusercontent.com/48157259/120971610-cdbb4480-c7a7-11eb-8c78-6f61747b0290.png)
![Untitled (1)](https://user-images.githubusercontent.com/48157259/120981096-458e6c80-c7b2-11eb-8a3c-22815db48765.png)
\- Develop Branch로 PR 요청  
\- PR 승인으로 merged되면 로컬 저장소에서 `git pull` 시행하여 동기화시킴


#### (3) PR후 로컬환경에서 branch 삭제하기
```bash
$ git branch -D feature/menu                # local branch 삭제
$ git push origin --delete feature/menu     # remote branch 삭제
$ git remote prune origin
```

