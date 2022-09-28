---
layout: post
title: brew로 old version 설치하기
categories: etc
tags: [etc]
---

최신 버전이 아닌 old 버전의 패키지를 설치 해야 할 때가 있다. 하지만 태그(ex `mysql@5.7`)를 제공하지 않는 패키지는 brew를 통해 설치할 수 없는데, 직접 설치 루비스크립트를 받아 파일을 통해 설치가 가능하다.  

1. homebrew-core 깃허브 이동
    - https://github.com/Homebrew/homebrew-core/tree/master/Formula

2. `Go to file`로 파일 탐색

3. 해당 패키지의 history에서 원하는 버전 선택
![](https://i.stack.imgur.com/A2eER.png)

4. Raw 버튼을 눌러 해당 파일로 이동
![](https://i.stack.imgur.com/WUYRZ.png)

5. `cmd+s`로 루비스크립트 다운로드

6. 커맨드를 통해 다운받은 루비스크립트를 실행해 패키지 설치
```cmd
$ brew install tflint.rb
```