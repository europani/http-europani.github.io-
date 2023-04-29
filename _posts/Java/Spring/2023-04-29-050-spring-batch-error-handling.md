---
layout: post
title: '[Batch] 반복 및 에러처리'
categories: Spring
tags: ['Spring Batch']
---

### 반복
- 특정 조건이 만족될 때까지 Job or Step을 반복하도록 구성 가능
- `RepeatOperation`을 사용
  - 구현체 : `RepeatTemplate`

#### 1. RepeatStatus
- 배치 처리가 끝났는지 판별하기 위한 Enum
- `CONTINUABLE` : 계속 진행
- `FINISHED` : 종료


#### 2. CompletionPolicy
- `RepeatTemplate`의 iterate()에서 반복을 중단할지 결정
- 실행 횟수 또는 완료시기, 오류 발생시 수행 할 작업에 대한 반복여부 결정
- **정상 종료**를 알리는데 사용

- 구현체
  - `SimpleLCompletionPolicy` : 스프링배치가 기본으로 사용
  - `CountingCompletionPolicy` 
  - `TimeoutTerminationnPolicy` 


#### 3. ExceptionHandler
- RepeatCallback 안에서 예외가 발생하면 RepeatTemplate 가 ExceptionHandler 를 참조해서 예외를 다시 던질지 여부 결정
- 예외를 받아서 다시 던지게 되면 반복 종료
- **비정상 종료** 를 알리는데 사용

- 구현체
  - `SimpleLimitExceptionHandler` : 스프링배치가 기본으로 사용
  - `LogOrRethrowExceptionHandler`
  - `RethrowOrThresholdExceptionHandler`

![image](https://user-images.githubusercontent.com/109575750/235282812-e9e6a7a8-b23f-4340-81a2-2b607bb786bd.png)