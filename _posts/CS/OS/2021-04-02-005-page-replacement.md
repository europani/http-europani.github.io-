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

### 2. FIFO(First In First Out)
- 메모리 가장 먼저 올라온 페이지를 먼저 교체
![](https://miro.medium.com/max/700/1*PisBTTZmXb2ZLHix7RdBCQ.png)

### 3. LRU(Least Recently Used)
- 가장 오래 전에 사용된 페이지를 교체
- FIFO보다 효율적
- 사용빈도 높음
- 링크드리스트로 구현, O(1)
![](https://miro.medium.com/max/700/1*2KmdY3wX68yaZnF6MwwTqg.png)

### 4. LFU(Least Frequently Used) 
- 가장 참조 횟수가 적은 페이지를 교체
- 초기에 많이 사용했지만 후기에 사용하지 않는 페이지가 있을 경우 계속 잔류하는 단점이 있음
- 힙으로 구현, O(logN)
![](https://miro.medium.com/max/700/1*nIY4ek2yu3jsND7na4AF-Q.png)

### 5. NUR(Not Used Recently) =NRU
- 최근에 사용 하지 않은 페이지를 교체 (like LRU)
  - LRU의 근사 알고리즘
- Clock 알고리즘, Second Chance Algorithm, NUR, NRU 등의 명칭으로 불린다
- 참조비트와 변형비트 사용
  - 참조비트(Reference Bit) : 페이지가 호출되지 않았을때 0, 호출되었을 때 1 (Read)
  - 변형비트(Modified Bit) : 페이지 내용이 변경되지 않았을 때 0, 변경되었을 때 1 (Write)
  
|참조비트|변형비트|교체순서|
|:---:|:---:|:---:|
|0|0|1|
|0|1|2|
|1|0|3|
|1|1|4|

![](https://user-images.githubusercontent.com/48157259/163664540-f228a293-d1d2-4c51-aa88-6162cf2359c7.png)
- 참조비트로 교체 대상 페이지 선정 : 참조비트가 0인 것을 찾을 때 까지 포인터 이동
- 이동하면서 참조비트를 1 -> 0으로 변경하면서 이동
- 참조비트가 0인 것을 찾으면 그 페이지를 교체
- 참조비트가 1이였지만 한바퀴를 다돌고와서 0으로 변경된 페이지로 포인터가 다시오면 그때 교체발생 
- 자주 사용하는 페이지는 이때도 참조비트가 1이다