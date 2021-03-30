---
layout: post
title: Github repository의 하위 폴더만 Clone하기
categories: Git
tags: [Git]
---

0\. 워크스페이스로 이동 + git bash 실행

1\. folder라는 새로운 폴더를 만든 다음 그 폴더 안에 clone (-n때문에 파일이 바로 clone되진 않음) + 폴더로 이동

```bash
git clone -n http://github.com/repository.git folder

cd folder
```

2\. Sparse Checkout 기능 활성화

```bash
git config core.sparseCheckout true
```

3\. checkout 하기 원하는 폴더나 파일 " "안에 입력 + >> .git/info/sparse-checkout

  -> " "안에 하위 폴더를 적을 때 github repository의 첫번째 하위폴더부터 적어주면 됨

```bash
echo "java/ch01" >> .git/info/sparse-checkout
```

4\. master 브랜치로 checkout

```bash
git checkout master
```