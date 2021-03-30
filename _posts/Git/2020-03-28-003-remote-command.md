---
layout: post
title: Git 명령어(remote)
categories: Git
tags: [Git]
---

remote add origin 주소 : origin이라는 이름으로 해당주소의 원격저장소를 로컬저장소에 등록   

      ex) git remote add origin [https://github.com/europani/Web.git](https://github.com/europani.git)

remote remove origin : 로컬저장소와 원격저장소 연결해제

remote -v : 등록된 원격저장소 확인

push \[remote명 branch명\] : commit한 자료를 원격저장소로 push함 (local->remote)        ex) git push \[origin master\]

pull : 자료를 원격저장소로부터 pull함 (remote->local)                                  ex) git pull

   (pull = fetch + merge)

clone : 해당주소의 원격저장소로부터 자료를 로컬저장소로 복제해옴.     

          로컬저장소로 복제만 해오지 tracked 되진 않음.(그냥 Copy&Paste)

      ex) git clone [https://github.com/europani/Web.git](https://github.com/europani/Web.git) \[바꾸고싶은 폴더명\]

fetch : 원격저장소로부터 자료를 pull해 오지만 HEAD라는 branch로 가져옴.       ex) git fetch

         즉, 로컬저장소에 덮어써지지 않음. 이후 merge 명령어로 로컬저장소의    ex) git merge FETCH\_HEAD

         자료와 합쳐야함.