---
layout: post
title: '[Batch] 스프링 배치(Spring Batch)'
categories: Spring
tags: ['Spring Batch']
---
### Spring Batch
- 배치(Batch) : 일괄 처리 방식을 의미
- 스프링 배치는 로깅/추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너뛰기, 리소스 관리 등 대용량 레코드 처리에 필수적인 기능을 제공
- 배치가 실패하여 작업 재시작을 하게 된다면 처음부터가 아닌 실패한 지점부터 실행
- 중복 실행을 막기 위해 성공한 이력이 있는 Batch는 동일한 Parameters로 실행 시 Exception이 발생

- 배치 작업이 필요한 이유
  - 대용량의 비지니스 데이터를 복잡한 작업으로 처리해야하는 경우
  - 특정한 시점에 스케줄러를 통해 자동화된 작업이 필요한 경우
  - 대용량 데이터의 포맷을 변경, 유효성 검사 등의 작업을 트랜잭션 안에서 처리 후 기록해야하는 경우


### 구조
![image](https://user-images.githubusercontent.com/109575750/216747019-b91c12ea-cb96-4f0c-99bf-55b1a6fb612e.png)

### Job
- 배치처리 과정을 하나의 단위로 만들어 놓은 객체
- 1개의 Job에는 1개 이상의 Step이 구성되야 한다 (Job:Step = `1:N`)
- 구현체 : `SimpleJob`, `FlowJob`

#### 1. JobLauncher
- Job과 JobParameter를 사용하여 Job을 실행시키는 객체
- 배치처리를 완료 한 다음에 Client에게 JobExecution을 반환한다 (동기적 실행)
    - 비동기적 실행도 가능함

```java
public interface JobLauncher {
    JobExecution run(Job job, JobParameters jobParameters)
}
```

#### 2. JobInstance
- Job의 논리적 실행단위 객체로써 고유하게 식별 가능한 작업실행
- JobName + JobParameter 가 같을 경우 동일한 JobInstance를 리턴해 Exception 발생 (중복불가)
- Job:JobInstance = `1:N`

#### 3. JobParameters
- Job 실행 시 사용되는 파라미터
- 여러 개의 JobInstance를 구분하기 위한 용도
- JobParameters:JobInstance = `1:1`

```java
public class JobParameters implements Serializable {
    private final Map<String,JobParameter> parameters;

	public JobParameters() {
		this.parameters = new LinkedHashMap<>();
	}
    ...
}
```

#### 4. JobExecution
- JobInstance의 실행시도 객체
- JobInstance 실행상태, 시작시간, 종료시간, 생성시간 등의 정보를 저장
- 실행상태가 `FAILED`이면 재실행 가능하며 새로운 `JobExecution`이 생성됨 
    - `COMPLITED`이면 재실행이 불가하며 실행시도시 `JobInstanceAlreadyCompleteException` 발생
- JobInstance:JobExecution = `1:N`

#### 5. JobRepository
- 모든 배치 정보를 갖고 있는 메커니즘 (메타데이터 저장소)
- Job이 실행되게 되면 JobRepository에 JobExecution과 StepExecution을 생성하게 되며 JobRepository에서 Execution 정보들을 DB에 저장하고 조회하며 사용한다

### Step
- Job의 배치처리 과정을 정의하고 순차적으로 실행되는 객체 (Job의 세부 과정)
- 구현체 : `TaskletStep`, `PartitionStep`, `JobStep`, `FlowStep`

```java
public interface Step {
    void execute(StepExecution stepExecution) throws JobInterruptedException;
}
```
 
#### 1. StepExecution
- Step의 실행시도 객체, Step별로 StepExecution이 생성됨
- JobExecution에 저장되는 정보 외에 read 수, write 수, commit 수, skip 수 등의 정보도 저장 
- JobExecution:StepExecution = `1:N`
- Job이 재시작되더라도 이미 성공된 Step은 스킵되고 실패한 Step만 실행된다 (Step의 `allowStartIfComplete=true`이면 스킵되지 않게 설정가능  )
- Step의 StepExecution이 하나라도 실패하면 JobExecution은 실패처리 된다
![image](https://user-images.githubusercontent.com/109575750/230754683-ca8b5eec-a3ee-4f66-8fde-843867b9118b.png)

### Tasklet
```java
public interface Tasklet {
    @Nullable
    RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception;
}

```

#### 1. ItemReader, ItemWriter, ItemProcesseor
- Step 과정에서 Item을 읽어 데이터를 처리한 다음 결과를 처리하는 객체

---

```gradle
implementation "org.springframework.boot:spring-boot-starter-batch"
```

```java
@Configuration
@RequiredArgsConstructor
@EnableBatchProcessing
public class ExampleJobConfig {

    public static final String JOB_NAME = "example";

    public JobBuilderFactory jobBuilderFactory;
    private final Step exampleStep;
    private final Step exStep;

    @Bean(JOB_NAME)
    public Job ExampleJob(){

        Job exampleJob = jobBuilderFactory
                .get(JOB_NAME)
                .incrementer(new UniqueRunIdIncrementer())
                .start(exampleStep)
                .next(exStep)
                .build();

        return exampleJob;
    }
}
```

```java
@Slf4j
@Component
@JobScope
@RequiredArgsConstructor
public class ExampleStepConfig {

    public static final String STEP_NAME = "example";

    public StepBuilderFactory stepBuilderFactory;


    @Bean(STEP_NAME)
    public Step ExampleStep(){

        Step exampleStep = stepBuilderFactory
                .get(STEP_NAME)
                .tasklet((contribution, chunkContext) -> {
                    log.info("Start Step!");
                    return RepeatStatus.FINISHED;
                })
                .build();

        return exampleStep;
    }

    @Bean("ex")
    public Step ExStep(){

        Step exampleStep = stepBuilderFactory
                .get("ex")
                .tasklet((contribution, chunkContext) -> {
                    log.info("End Step!");
                    return RepeatStatus.FINISHED;
                })
                .build();

        return exampleStep;
    }
}
```