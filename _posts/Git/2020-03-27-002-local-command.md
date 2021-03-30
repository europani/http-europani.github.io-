---
layout: post
title: Git 명령어(Local)
categories: Git
tags: [Git]
---

모든 명령에 git을 붙이고 시작함.

init : 현재 디렉토리를 Git Repository로 설정(.git 폴더 생성)    ex) git init

add : 선택한 폴더,파일을 Working Tree에서 Staging Area에 추가  ex) git add hello.txt

   ☞ . : 현재 디렉토리의 모든파일을 추가                          ex) git add .

commit : 버전을 만듦. Staging Area의 모든 파일을 Git Repository로 올림

   ☞ -m : 바로 메시지작성 가능.(message)                         ex) git commit -m "version1"

       -am : add와 commit을 동시에 함.(add + message)        ex) git commit -am "version1"

       --amend : 커밋내용 수정, push전까지만 사용가능         ex) git commit --amend

status : git의 상태표기, Git Repository에서만 사용가능

log : 로그(기록)을 보여줌

   ☞ --stat : 자세히 로그를 보여줌                                    ex) git log --stat

       -p : 변경내용까지 보여줌.

       --graph : 그래프로 보여줌

diff : 파일에서 변경내용을 보여줌.

checkout : ID가 가르키는 버전(과거버전)으로 돌아감.            ex) git checkout c706e06c284c63d239c3aff62f8452a9

   ☞ master : 최신 버전으로 다시 돌아감                            ex) git checkout master

restore : working tree 비우기 (커밋전 수정한 파일 원상복구)

reset : commit 취소, ID가 가르키는 버전(과거버전)으로 돌아감(옵션에 따라 다르게)

 ① ID를 이용한 reset

     ex) git reset \[--mixed\] c706e06c284c63d239c3aff62f8452a9,   

 ② 파일명을 이용한 reset 

     ex) git reset --hard hello.txt

 ③ 현재위치(HEAD)를 기준(상대참조)으로 한 reset

     ex) git reset --hard HEAD~3 (현재위치로부터 3개전의 commit으로 reset, HEAD~1 = HEAD^)

  ☞ --soft : commit후 unmodified -> commit전 staged 상태로 회귀

      --mixed : commit후 unmodified -> commit전 modified 상태로 회귀\[default\]

      --hard : commit후 unmodified -> commit전 unmodified 상태로 회귀(수정한것을 완전히 날림)

revert : ID가 가르키는 버전(과거버전)으로 돌아가면서 있던버전을 기록으로 남김.

   ex) git revert c706e06c284c63d239c3aff62f8452a9

        git revert HEAD (바로이전으로 돌아감)

![](https://blog.kakaocdn.net/dn/RVvKW/btqHJoyKYwX/z7BUhKyBtLeLZrBi8qS6cK/img.png)