---
layout: post
title: 포그라운드(Foreground), 백그라운드(Background)
categories: OS
tags: [OS, CS]
---

- 포그라운드(Foreground) : 앞쪽에서 처리되는 프로세스, 결과출력을 기다림
```bash
$ sleep 100
```
- 백그라운드(Background) : 뒤쪽에서 처리되는 프로세스, 결과출력을 기다리지 않고 다른작업 가능
```bash
$ sleep 100 &
```

#### 작업목록 출력(jobs)

|상태|설명|
|:---:|:---:|
|Running|실행중|
|Stopped|일시중단|
|Done|정상종료|
|Terminated|비정상종료|


#### 작업 제어
##### 1. 작업 전환 : 포그라운드 ↔ 백그라운드  
   (1) `bg %작업번호` : 포그라운드 → 백그라운드 전환
   ```bash
   $ jobs
   [1]+ Stopped sleep 100
   $ bg %1

   $ jobs
   [1]+ Running sleep 100 &
   ```


   (2) `fg %작업번호` : 백그라운드 → 포그라운드 전환
   ```bash
   $ jobs
   [1]+ Running sleep 100 &
   $ fg %1
   ```


##### 2. 작업 일시중단
   - 실행중인 작업을 일시중단시킴
   - 포그라운드 일시중단 : `Ctrl + Z`
   - 백그라운드 일시중단 : `stop %작업번호`
  
   ```bash
   $ sleep 100 &
   $ jobs
   [1]+ Running sleep 100 &
   $ stop %1

   $ jobs
   [1]+ Stopped sleep 100 &
   ```

##### 3. 작업 종료
   - 포그라운드 작업종료 : `Ctrl + C`
   - 백그라운드 작업종료 : `kill [PID or %작업번호]`


##### 4. 로그아웃 후에도 백그라운드 작업 계속 실행 
   - `nohup 명령어 &`
   - nohup : no hang up
  
   ```bash
   $ nohup sleep 100 &
   ```


  

   