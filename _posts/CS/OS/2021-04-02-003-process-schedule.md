---
layout: post
title: 프로세스 스케줄링
categories: OS
tags: [OS, CS]
---

CPU를 사용하려는 프로세스들 사이의 우선순위를 관리하는 작업

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style4"><tbody><tr><td style="width: 30.9303%;">대기시간</td><td style="width: 69.0697%;">프로세서에 도착(프로세스 입력)해서 실행대기큐에 대기한 시간</td></tr><tr><td style="width: 30.9303%;"><span style="color: #333333;">서비스시간(실행시간)<br>(Burst time)</span></td><td style="width: 69.0697%;"><span style="color: #333333;">프로세스 시작~끝(결과도출) 걸린시간</span></td></tr><tr><td style="width: 30.9303%;"><span style="color: #333333;">반환시간 or 응답시간</span><br><span style="color: #333333;">(Turnaround time or Response time)</span></td><td style="width: 69.0697%;"><span style="color: #333333;">프로세스가 입력~ 결과도출 걸린 시간</span><br><span style="color: #333333;">대기시간 + 서비스시간</span></td></tr><tr><td style="width: 30.9303%;"><span style="color: #333333;">도착 시간(at)</span></td><td style="width: 69.0697%;">프로세서에 도착한 시간</td></tr><tr><td style="width: 30.9303%;">종료 시간(at)</td><td style="width: 69.0697%;">결과가 도출된 시간</td></tr></tbody></table>

### 비선점형 스케줄링 
: CPU를 할당받으면 작업이 끝나 CPU가 반환될 때 까지 다른 프로세스가 CPU 점유 불가능

- 장점 : 모든 프로세스가 공정하게 CPU 사용, 응답시간 예측 쉬움, 오버헤드 적음
- 단점 : 긴급한 프로세스를 빠르게 처리할 수 없음

#### 1. FCFS = FIFO (First Come First Served)
: CPU 요청을 먼저 한 순서대로 처리

#### 2. SJF(Shortest Job First)
: 도착 시점에 따라 그 당시 가장 작은 서비스시간을 갖은 프로세스에게 할당, 기아현상 발생 (Aging으로 예방)
 - 기아현상(starvation) : 한 프로세스가 처리되지 못하고 계속 연기

#### 3. HRN (Highest Response Ratio Next) 
: 응답률이 가장 높은 프로세스에게 할당 
 - 응답률 : (대기시간 + 서비스시간) / 서비스 시간

#### 4. 우선순위 (Priority) 
: 특정 규칙에 따라 프로세스에 우선순위를 매겨 순서대로 할당

#### 5. 기한부 (Deadline) 
: 일정 시간동안 프로세스가 완료되지 않으면 삭제하고 다시 실행


### 선점형 스케줄링 
: 우선순위가 높은 프로세스가 현재 프로세스를 중단시키고 CPU 점유 가능

- 장점 : 긴급한 프로세스 우선 처리 가능
- 단점 : 많은 오버헤드 발생

#### 1. RR (Round Robin)
: 시분할 시스템 - 프로세스들에게 순서대로(FIFO) 같은 시간(Time Slice) 동안 CPU할당

#### 2. SRT (Shortest Remaining Time First)
: 가장 짧은 시간이 소요되는 프로세스에게 할당, 더 짧게 소요되는 프로세스가 큐에 추가되면 선점

#### 3. 다단계 큐
: 프로세스들을 분류해서 다양한 스케줄링 방법을 사용하는 다수의 작업큐로 할당
- 한번 특정 큐로 할당된 프로세스는 다른 큐로 이동 불가

#### 4. 다단계 피드백 큐
: 다단계 큐에서 큐에있는 프로세스가 다른 큐로 이동할 수 있도록 기능을 추가
