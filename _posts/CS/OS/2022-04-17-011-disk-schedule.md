---
layout: post
title: 디스크 스케줄링
categories: OS
tags: [OS, CS]
---
- 운영체제가 프로세스들이 디스크 접근요청을 받았을 때 우선순위를 정해 관리하는 것
- 디스크에서 원하는 정보를 읽어오기 위한 접근시간 중에서 **탐색시간**이 제일 오래 걸린다

### 1. FCFS (First Come First Served)
- 요청 순서대로 처리하는 알고리즘
- 이동 거리가 상당히 길다
![](https://user-images.githubusercontent.com/48157259/163900713-f0094d5e-cede-411b-838d-84036e3f5680.png)

### 2. SSTF (Shortest Seek Time First)
- 현재 헤드위치에서 가장 가까운 요청을 먼저 처리하는 알고리즘
- 기아현상이 발생
![](https://user-images.githubusercontent.com/48157259/163900859-75a4cbdc-61f9-4976-be89-49e3c6d48792.png)

### 3. SCAN
- 헤드가 한쪽 끝에 도달하면 반대방향으로 향하며 양방향으로 왕복운동을 하는 알고리즘
- 엘리베이터 스케줄링(왕복운동)
- 단점 : 트랙 위치에 따라 대기시간이 다르다 (가운데 트랙은 대기시간이 짧고 양 끝 트랙은 길다)
![](https://user-images.githubusercontent.com/48157259/163900985-1cb04e25-b377-4584-b797-32edf466b52c.png)

### 4. C-SCAN
- SCAN의 단점을 보완하기 위해 한방향으로만 진행
- SCAN 보다 균등한 대기시간을 제공
![](https://user-images.githubusercontent.com/48157259/163901348-abf348bb-a53e-4e95-a10b-77a644b5fa7d.png)

### 5. LOOK
- SCAN이 변형된 알고리즘
- 진행방향에 더 이상의 요청 트랙이 없을 때도 끝을 도달하는 SCAN과는 다르게 바로 진행방향을 바꾼다
![](https://user-images.githubusercontent.com/48157259/163901440-1eb42e96-1f28-4d50-b087-bd581f9880e4.png)

### 6. C-LOOK
- C-SCAN이 변형된 알고리즘
- 진행방향에 더 이상의 요청 트랙이 없을 때도 끝을 도달하는 C-SCAN과는 다르게 바로 다음으로 진행한다
![](https://user-images.githubusercontent.com/48157259/163901502-2c26eb8b-f64a-492a-ae4e-b8567461e56b.png)