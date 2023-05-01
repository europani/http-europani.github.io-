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


### 오류제어
- FaultTolerant
- Job 실행 중에 오류가 발생할 경우 장애를 처리하기 위한 기능을 제공
- 오류가 발생해도 Step을 즉시 종료 시키지 않도록 `Skip`, `Retry` 사용 
- Retry 먼저 실행 후 Skip을 실행한다
- Exception 발생시 Chunk의 첫단계로 간다

![image](https://user-images.githubusercontent.com/109575750/235405685-0a200c99-016d-4ab4-91ba-1120db281762.png)

- Skip
  - 데이터를 처리하는 동안 설정된 Exception이 발생할 경우, 해당 데이터 처리를 건너뛰는 기능
  - `ItemReader`, `ItemProcessor`, `ItemWriter`에 적용 가능
  - skip count는 Reader, Processor, Writer에서 공유한다
  - skip된 item을 제외하고 chunk를 구성한다  
    ex) skip:3 / chunk = 1, 2, 4, 5, 6

  ![image](https://user-images.githubusercontent.com/109575750/235409398-6f8f6990-117c-4739-94b9-db7213010207.png)

- Retry
  - 데이터를 처리하는 동안 설정된 Exception이 발생할 경우, 해당 데이터 처리를 재시도하는 기능
  - `ItemProcessor`, `ItemWriter`에 적용 가능
  - Retry는 Skip과는 다르게 동작시 Chunk의 `items`가 변경되지 않는다
  
  - retry 조건 충족시 `RetryCallBack`에 의해 재시도
  - retry 조건 미충족시 `RecoveryCallBack`으로 후속작업 가능

  ![image](https://user-images.githubusercontent.com/109575750/235409453-abdc19eb-ff6e-40c1-b094-0fa966337554.png)

  - BackOffPolicy[Optinal] : 재시도시 지연시간을 설정할 수 있다 

```java
public class ExampleStepConfig {

    public final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Step chunkStep(){
        Step chunkStep = stepBuilderFactory     // StepBuilderFactory
                .get("chunkStep")               // StepBuilder
                .<String, String>chunk(10)      // SimpleStepBuilder
                .reader(new CustomReader())
                .processor(new CustomProcessor())
                .writer(new CustomWriter())
                .faultTolerant()                // FaultTolerantStepBuilder
                .skip(CustomException.class)
                .skipLimit(2)
          //    .retry(CustomException.class)
          //    .retryLimit(2)
                .retryPolicy(retryPolicy())
                .build();                       // TaskletStep

        return chunkStep;

    @Bean
    public RetryPolicy retryPolicy() {
      Map<Class<? extends Throwable>, Boolean> exceptionClass = new HashMap<>();
      exceptionClass.put(CustomException.class, true);

      SimpleRetryPolicy simpleRetryPolicy = new SimpleRetryPolicy(2, exceptionClass);

      return simpleRetryPolicy;
    }
}
```