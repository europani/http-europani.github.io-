---
layout: post
title: 디스크 스케줄링
categories: OS
tags: [OS, CS]
---

1. FCFS (First Come First Served)
- 이동 거리가 상당히 길다

2. SSTF (Shortest Seek Time First)
- 기아현상이 발생

3. SCAN
- 엘리베이터 스케줄링(왕복운동)
- 단점 : 트랙 위치에 따라 대기시간이 다르다 (가운데 트랙은 대기시간이 짧고 양 끝 트랙은 길다)

4. C-SCAN
- 한방향으로만 진행
- SCAN 보다 균등한 대기시간을 제공

5. LOOK
- SCAN이 변형된 알고리즘
- 진행방향에 더 이상의 요청 트랙이 없을 때도 끝을 도달하는 SCAN과는 다르게 바로 진행방향을 바꾼다

6. C-LOOK
- C-SCAN이 변형된 알고리즘
- 진행방향에 더 이상의 요청 트랙이 없을 때도 끝을 도달하는 C-SCAN과는 다르게 바로 다음으로 진행한다