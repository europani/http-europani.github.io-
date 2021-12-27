---
layout: post
title: Git 명령어(branch&conflict)
categories: Git
tags: [Git]
---

#### branch
- branch 목록 확인(local만)
  - `-r` : branch 목록 확인(remote만)
  ```git
  $ git branch -r
    ```

  - `-a` : branch 목록 확인(local + remote)
  ```git
  $ git branch -a
    ```

  - `-v` : 상세보기
  ```git
  $ git branch -v
  ```

#### branch 브랜치명
- 새로운 branch 생성
```git
$ git branch test
```

  - `-d` : 기존의 branch 제거
  ```git
  $ git branch -d test
  ```

#### checkout 
- 생성된 다른 branch로 이동
```git
$ git checkout test
```

  - `-b` : 새로운 branch를 생성하고 바로 이동
  ```git
  $ git checkout -b test
  ```

#### merge 병합할브랜치명
- 현재 branch에 병합할 branch를 병합함 (HEAD는 현재 작업중인 브랜치를 의미함)

ex) master 브랜치로 돌아와서 작업한 test branch를 master 브랜치에서 병합함  
```git
$ git checkout master
$ git merge test
```

#### rebase 병합할브랜치명 
- 현재 브랜치가 가리키는 커밋을 다른 브랜치가 가리키는 커밋 뒤에 그대로 잇는 작업
- 즉, 현재 브랜치에서 작업하던 내용을 병할할브랜치로 들고가서 병합

ex) test 브랜치에서 작업이 끝나 master 브랜치로 병합함
```git
$ git branch test
$ git checkout test
....(작업)
$ git rebase master
```