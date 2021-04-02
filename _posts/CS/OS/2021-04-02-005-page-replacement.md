---
layout: post
title: 페이지 교체 알고리즘
categories: OS
tags: [OS, CS]
---

### 1. OPT(OPtimal replacement)
- 앞으로 가장 오랫동안 사용하지 않을 페이지를 교체
- 프로세스가 앞으로 사용할 페이지를 알아야 가능함. (즉, 알고리즘 구현 불가능)
- 실제 구현이 불가능하기 때문에 다른 알고리즘과 비교 연구 목적으로 주로 사용됨
- 가장 페이지 교체수가 적은 알고리즘
![](https://miro.medium.com/max/700/1*MHoq4CVbRsKyXwycQanhnA.png)

### 2. FIFO(First IN First Out)
- 메모리 가장 먼저 올라온 페이지를 먼저 교체
![](https://miro.medium.com/max/700/1*PisBTTZmXb2ZLHix7RdBCQ.png)

### 3. LRU(Least Recently Used)
- 가장 오래 전에 사용된 페이지를 교체
- FIFO보다 효율적
- 사용빈도 높음
![](https://miro.medium.com/max/700/1*2KmdY3wX68yaZnF6MwwTqg.png)

### 4. LFU(Least Frequently Used) 
- 가장 참조 횟수가 적은 페이지를 교체
- 초기에 많이 사용했지만 후기에 사용하지 않는 페이지가 있을 경우 계속 잔류하는 단점이 있음
![](https://miro.medium.com/max/700/1*nIY4ek2yu3jsND7na4AF-Q.png)

### 5. NUR(Not Used Recently) 
- 최근에 사용 하지 않은 페이지를 교체 (= LRU)
- 참조비트와 변형비트 사용
  - 참조비트 : 페이지가 호출되지 않았을때 0, 호출되었을 때 1
  - 변형비트 : 페이지 내용이 변경되지 않았을 때 0, 변경되었을 때 1
  
|참조비트|변형비트|교체순서|
|:---:|:---:|:---:|
|0|0|1|
|0|1|2|
|1|0|3|
|1|1|4|