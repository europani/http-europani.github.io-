---
layout: post
title: '[Batch] 스프링 배치(Spring Batch)'
categories: Spring
tags: ['Spring Batch']
---
## Spring Batch
- 배치(Batch) : 일괄 처리 방식을 의미
- 스프링 배치는 로깅/추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너뛰기, 리소스 관리 등 대용량 레코드 처리에 필수적인 기능을 제공
- 배치가 실패하여 작업 재시작을 하게 된다면 처음부터가 아닌 실패한 지점부터 실행
- 중복 실행을 막기 위해 성공한 이력이 있는 Batch는 동일한 Parameters로 실행 시 Exception이 발생

- 배치 작업이 필요한 이유
  - 대용량의 비지니스 데이터를 복잡한 작업으로 처리해야하는 경우
  - 특정한 시점에 스케줄러를 통해 자동화된 작업이 필요한 경우
  - 대용량 데이터의 포맷을 변경, 유효성 검사 등의 작업을 트랜잭션 안에서 처리 후 기록해야하는 경우

```gradle
implementation "org.springframework.boot:spring-boot-starter-batch"
```

## 구조
![image](https://user-images.githubusercontent.com/109575750/216747019-b91c12ea-cb96-4f0c-99bf-55b1a6fb612e.png)

## ExecutionContext
- 프레임워크에서 Execution의 상태를 저장하는 공유 객체
- Job 재 시작시 이미 처리한 Row 데이터는 건너뛰고 이후로 수행하도록 할 때 상태 정보를 활용한다
- 종류 : JobExecution, StepExecution

- JobExecution : 각 Job에 생성되며 Job 간에 공유되지 않는다. 다만, Job에 포함된 Step 간에는 공유된다
- StepExecution : 각 Step에 생성되며 Step 간에 공유되지 않는다.

```java
public class ExecutionContext {
    Map<String, Object> map = new ConcurrentHashMap();
} 
```

## Batch Scope
- Scope : 스프링 컨테이너에서 빈이 관리되는 범위
  - ex) singleton, prototype, request, session, application 등

- Spring Batch에서만 제공하는 스코프가 존재한다
  - `@JobScope`, `@StepScpoe`
  - Job 과 Step 의 빈 생성과 실행에 관여하는 스코프
  - 해당 스코프가 선언되면 빈이 생성이 어플리케이션 구동시점이 아닌 빈의 실행시점에 이루어진다 (Lazy Binding)
  - **어플리케이션 구동시점에 프록시 객체를 생성해 놓고 실행시점`step()`에 실제 빈을 생성하면서 value를 바인딩한다**

- `@Value`를 사용해 빈의 실행시점에 값을 주입할 수 있다
```java
@Value("#{jobParameters[파라미터명]}")
@Value("#{jobExecutionContext[파라미터명]”})
@Value("#{stepExecutionContext[파라미터명]”})
```
- @Value 를 사용할 경우 @Bean에 @JobScope, @StepScope 를 정의하지 않으면 오류 발생

### 1. @JobScope
- Step에 정의
- @Value : jobParameter, jobExecutionContext 사용가능

### 2. @StepScope
- Tasklet이나 ItemReader, ItemWriter, ItemProcessor에 정의
- @Value : jobParameter, jobExecutionContext, stepExecutionContext 사용가능



## Job
- 배치처리 과정을 하나의 단위로 만들어 놓은 객체
- 1개의 Job에는 1개 이상의 Step이 구성되야 한다 (Job:Step = `1:N`)
- 구현체 : `SimpleJob`, `FlowJob`

### 1. JobLauncher
- Job과 JobParameter를 사용하여 Job을 실행시키는 객체
- 배치처리를 완료 한 다음에 Client에게 JobExecution을 반환한다 (동기적 실행)
    - 비동기적 실행도 가능함

```java
public interface JobLauncher {
    JobExecution run(Job job, JobParameters jobParameters)
}
```

### 2. JobInstance
- Job의 논리적 실행단위 객체로써 고유하게 식별 가능한 작업실행
- JobName + JobParameter 가 같을 경우 동일한 JobInstance를 리턴해 Exception 발생 (중복불가)
- Job:JobInstance = `1:N`

### 3. JobParameters
- Job 실행 시 사용되는 파라미터
- 여러 개의 JobInstance를 구분하기 위한 용도
- JobParameters:JobInstance = `1:1`

```java
public class JobParameters implements Serializable {
    private final Map<String, JobParameter> parameters;

    public JobParameters() {
        this.parameters = new LinkedHashMap<>();
    }
    ...
}
```

### 4. JobExecution
- JobInstance의 실행시도 객체
- JobInstance 실행상태, 시작시간, 종료시간, 생성시간 등의 정보를 저장
- 실행상태가 `FAILED`이면 재실행 가능하며 새로운 `JobExecution`이 생성됨 
    - `COMPLITED`이면 재실행이 불가하며 실행시도시 `JobInstanceAlreadyCompleteException` 발생
- JobInstance:JobExecution = `1:N`

### 5. JobRepository
- 모든 배치 정보를 갖고 있는 메커니즘 (메타데이터 저장소)
- Job이 실행되게 되면 JobRepository에 JobExecution과 StepExecution을 생성하게 되며 JobRepository에서 Execution 정보들을 DB에 저장하고 조회하며 사용한다


```java
@Configuration
@RequiredArgsConstructor
@EnableBatchProcessing
public class ExampleJobConfig {

    public static final String JOB_NAME = "example";

    private final JobBuilderFactory jobBuilderFactory;
    private final Step taskStep;
    private final Step chunkStep;

    @Bean(JOB_NAME)
    public Job ExampleJob(){

        Job exampleJob = jobBuilderFactory  // JobBuilderFactory
                .get(JOB_NAME)              // JobBuilder
                .start(taskStep)         // SimpleJobBuilder
                .next(chunkStep)
 //             .incrementer(new UniqueRunIdIncrementer())
                .incrementer(new CustomJobParametersIncrementer())
                .validator(new CustomJobParametersValidator())
                .preventRestart(false)              // default : true
                .build();                   // SimpleJob

        return exampleJob;
    }
}
```

- Validator 

```java
public class CustomJobParametersValidator implements JobParametersValidator {

    @Override
    public void validate(JobParameters parameters) throws JobParametersInvalidException {
        if (parameters.getString("name") == null) {
            throw new JobParametersInvalidException();
        }
    }
}
```

- Incrementer

```java
public class CustomJobParametersIncrementer implements JobParametersIncrementer {
    static final SimpleDateFormat format = new SimpleDateFormat("yyyyMMdd-hhmmss");

    @Override
    public JobParameters getNext(JobParameters parameters) {
        String id = format.format(new Date());
        return new JobParametersBuilder().addString("run.id", id).toJobParameters();
    }
}
```

## Step
- Job의 배치처리 과정을 정의하고 순차적으로 실행되는 객체 (Job의 세부 과정)
- 구현체 : `TaskletStep`, `PartitionStep`, `JobStep`, `FlowStep`

```java
public interface Step {
    void execute(StepExecution stepExecution) throws JobInterruptedException;
}
```

### 1. StepExecution
- Step의 실행시도 객체, Step별로 StepExecution이 생성됨
- JobExecution에 저장되는 정보 외에 read 수, write 수, commit 수, skip 수 등의 정보도 저장 
- JobExecution:StepExecution = `1:N`
- Job이 재시작되더라도 이미 성공된 Step은 스킵되고 실패한 Step만 실행된다 (Step의 `allowStartIfComplete=true`이면 스킵되지 않게 설정가능  )
- Step의 StepExecution이 하나라도 실패하면 JobExecution은 실패처리 된다
![image](https://user-images.githubusercontent.com/109575750/230754683-ca8b5eec-a3ee-4f66-8fde-843867b9118b.png)


- StepBuilder
  - 호출하는 메서드에 따라 사용하는 하위 StepBuilder가 다르며 그에 따른 Step이 생성된다

```java
public class StepBuilder extends StepBuilderHelper<StepBuilder> {

    // Tasklet 사용
    public TaskletStepBuilder tasklet(Tasklet tasklet) {
        return new TaskletStepBuilder(this).tasklet(tasklet);
    }

    // Chunk 기반의 Tasklet 사용
    public <I, O> SimpleStepBuilder<I, O> chunk(int chunkSize) {
        return new SimpleStepBuilder<I, O>(this).chunk(chunkSize);
    }
    public <I, O> SimpleStepBuilder<I, O> chunk(CompletionPolicy completionPolicy) {
        return new SimpleStepBuilder<I, O>(this).chunk(completionPolicy);
    }

    // PartitionStep
    public PartitionStepBuilder partitioner(String stepName, Partitioner partitioner) {
        return new PartitionStepBuilder(this).partitioner(stepName, partitioner);
    }
    public PartitionStepBuilder partitioner(Step step) {
        return new PartitionStepBuilder(this).step(step);
    }

    // JobStep
    public JobStepBuilder job(Job job) {
        return new JobStepBuilder(this).job(job);
    }

    // FlowStep
    public FlowStepBuilder flow(Flow flow) {
        return new FlowStepBuilder(this).flow(flow);
    }
}
```

```java
@Slf4j
@Component
@JobScope
@RequiredArgsConstructor
public class ExampleStepConfig {

    public final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Step taskStep(){
        Step taskStep = stepBuilderFactory     // StepBuilderFactory
                .get("taskStep")               // StepBuilder
                .tasklet((contribution, chunkContext) -> {  // TaskletStepBuilder
                    log.info("taskStep is executed!");
                    return RepeatStatus.FINISHED;
                })
                .build();                       // TaskletStep

        return taskStep;
    }

    @Bean
    public Step chunkStep(){
        Step chunkStep = stepBuilderFactory     // StepBuilderFactory
                .get("chunkStep")               // StepBuilder
                .<String, String>chunk(10)      // SimpleStepBuilder
                .reader(new CustomReader())
                .processor(new CustomProcessor())
                .writer(new CustomWriter())
                .build();                       // TaskletStep

        return chunkStep;
    }
}
```

## Tasklet
- Step 내에서 실행되는 객체, 주로 단일 task를 수행
- `TaskletStep`에 의해 반복적으로 실행(While loop)되며 반환값에 따라 실행/종료 된다
  - `RepeatStatus.CONTINUABLE` : 반복
  - `RepeatStatus.FINISHED` : 종료
- Step:Tasklet = `1:1`

```java
public interface Tasklet {
    @Nullable
    RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception;
}
```
![image](https://user-images.githubusercontent.com/109575750/232190334-20a17381-27b9-45ac-a2fb-ef36aaceac0d.png)

### 1. Task 기반
- 단일 작업 기반으로 처리하는 방식
![image](https://user-images.githubusercontent.com/109575750/232186883-8e9b2f79-b155-46ff-ad97-9dee19f10585.png)

### 2. Chunk 기반 (COT)
- Chunk기반 tasklet의 구현체 `ChunkOrientedTasklet` 사용
- 하나의 큰 덩어리를 N개씩 나누어 처리하는 방식
- Chunk 단위로 트랜잭션을 처리한다
![image](https://user-images.githubusercontent.com/109575750/232186927-13a042e0-2310-41cf-8e6c-a511058fb6f9.png)

```java
public class Chunk<W> implements Iterable<W> {

    private List<W> items = new ArrayList<>();
    private List<SkipWrapper<W>> skips = new ArrayList<>();
    private List<Exception> errors = new ArrayList<>();

    public void add(W item) {
        items.add(item);
    }
    public class ChunkIterator implements Iterator<W> {
        ...
    }
}
```

- ItemReader, ItemWriter, ItemProcesseor
  - Step 과정에서 Item을 읽어 데이터를 처리한 다음 결과를 처리하는 객체
  - `ItemReader`와 `ItemProcesseor`는 Chunk 내의 개별 item을 처리하지만 `ItemWriter`는 Chunk 모든 items를 일괄 처리 한다
  - `ItemReader`와 `ItemWriter`는 필수요소 지만 `ItemProcesseor`는 선택요소이다

#### 1. ItemReader
  - input 데이터를 읽어오는 인터페이스

```java
public interface ItemReader<T> {
    T read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException;
}
```

- 구현체
  - `FlatFileItemReader` : txt, csv 등 파일
  - `JsonItemReader` : json 파일
  - `MultiResourceItemReader` : 여러 개의 파일 조합
  - `JdbcCursorItemReader` : JDBC Cursor
  - `JpaCursorItemReader` : JPA Cursor 
  - `JdbcPagingItemReader` : JDBC Paging
  - `JpaPagingItemReader` : JPA Paging
  - `SynchronizeditemStreamReader` : Thread-safe ItemReader
  - `CustomItemReader` : Custom ItemReader

\* JDBC
- Cursor Base
  - 현재 행에 커서를 유지하며 다음 데이터를 호출하면 다음 행으로 커서를 이동하며 데이터 반환을 이루는 Streaming 방식의 I/O
  - DB connection이 연결되면 배치 처리가 완료될 때까지 데이터를 읽어온다 (Timeout 시간이 충분히 길 필요가 있음)
  - 모든 결과를 메모리에 올리기 때문에 메모리 사용량이 많다
  - Thread-safe 하지 않다

- **Paging Base**
  - 페이지 단위로 데이터를 조회하는 방식으로 Page Size 만큼 한번에 데이터를 가지고 온 다음 한 개씩 읽는다 (Offset, Limit 사용)
  - 한 페이지를 읽을 때마다 Connection을 맺고 끊는다
  - 페이징 단위의 결과만 메모리에 올려 메모리 사용량이 적다
  - Thread-safe 하다

#### 2. ItemWriter
  - Chunk 단위로 데이터를 받아 일괄 출력하는 인터페이스
  - 출력이 완료되면 트랜잭션이 종료되고 새로운 Chunk 단위 프로세스로 이동한다

```java
public interface ItemWriter<T> {
    void write(List<? extends T> items) throws Exception;
}
```

- 구현체
  - `FlatFileItemWriter` : txt, csv 등 파일
  - `JsonFileItemWriter` : json 파일
  - `JdbcBatchItemWriter` : JDBC
    - JDBC Batch 기능을 사용해 bulk update 처리
  - `JpaItemWriter` : JPA
    - Entity를 하나씩 chunk 크기만큼 update 처리
  - `CustomItemWriter` : Custom ItemWriter

#### 3. ItemProcessor
- 데이터를 출력하기 전에 데이터를 가공, 변형, 필터링 하는 역할

- 구현체
  - `CompositeItemProcessor` : ItemProcessor들을 체이닝 실행
  - `ClassifierCompositeItemProcessor` : 라우팅으로 ItemProcessor들 중 하나를 호출
  
```java
public interface ItemProcessor<I, O> {
    O process(@NonNull I item) throws Exception;
}
```
  
![image](https://user-images.githubusercontent.com/109575750/232303769-922b4275-7acc-4ffe-8f8e-0f17dc1c3ecd.png)