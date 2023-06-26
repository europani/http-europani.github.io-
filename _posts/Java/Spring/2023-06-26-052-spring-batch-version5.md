---
layout: post
title: '[Batch] Spring Batch 5 적용'
categories: Spring
tags: ['Spring Batch']
---

Spring Boot 3(=Spring Framework 6)부터 `Spring Batch 5` 버전을 사용하게 업데이트 되었다.  
Batch 5에 변경점이 많이 생겨 기존의 4버전과 다른 부분이 많이 생겼다.  
새로운 버전을 적용하면서 변경점에 대해 정리해보자.

### 적용한 변경점
1\. @EnableBatchProcessing
- 5버전부터는 더이상 해당 어노테이션이 필수가 아니게 되었다. 이 어노테이션은 4버전까지는 Batch와 관련된 Bean을 등록하게 해주는 필수 어노테이션이었지만 이젠 사용하지 않아도 빈 등록을 해준다
- 다만, BatchAutoConfiguration에 `@ConditionalOnMissingBean`이 추가되어 필요에 의해 해당 어노테이션을 사용해 default의 datasource나 transaction manager를 변경하면 `JobLauncherApplicationRunner`, `BatchDataSourceScriptDatabaseInitializer` 등의 빈 또한 등록되지 않기 때문에 수동으로 빈을 등록해줘야 한다 (아래 참조)

```java
...
@ConditionalOnMissingBean(value = DefaultBatchConfiguration.class, annotation = EnableBatchProcessing.class)
...
public class BatchAutoConfiguration {
  ...
}
```

2\. JobBuilderFactory, StepBuilderFactory deprecated
- 더이상 빌더 팩토리를 사용하지 않고 `JobBuilder`, `StepBuilder`를 사용한다

```java
// Sample with v4
@Configuration
@EnableBatchProcessing
public class MyJobConfig {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Bean
    public Job myJob(Step step) {
        return this.jobBuilderFactory.get("myJob")
                .start(step)
                .build();
    }
}
// Sample with v5
@Configuration
@EnableBatchProcessing
public class MyJobConfig {

    @Bean
    public Job myJob(JobRepository jobRepository, Step step) {
        return new JobBuilder("myJob", jobRepository)
                .start(step)
                .build();
    }
}
```

3\. JobRepository, TransactionManager 명시적으로 변경
- 내부적으로 필요한 객체를 명시적으로 표시해 주는 방식으로 변경되었다

```java
// Sample with v4
@Configuration
@EnableBatchProcessing
public class MyStepConfig {

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Step myStep() {
        return this.stepBuilderFactory.get("myStep")
                .tasklet(..) // or .chunk()
                .build();
    }
}
// Sample with v5
@Configuration
@EnableBatchProcessing
public class MyStepConfig {

    @Bean
    public Tasklet myTasklet() {
       return new MyTasklet();
    }

    @Bean
    public Step myStep(JobRepository jobRepository, Tasklet myTasklet, PlatformTransactionManager transactionManager) {
        return new StepBuilder("myStep", jobRepository)
                .tasklet(myTasklet, transactionManager) // or .chunk(chunkSize, transactionManager)
                .build();
    }
}
```

<hr/>

### build.gradle

```gradle
dependencies {
  ...
  implementation("org.springframework.boot:spring-boot-starter-batch")
  testImplementation("org.springframework.batch:spring-batch-test")
  ...
}
```

### application.yaml

```yaml
spring:
  batch:
    job:
      name: ${job.name:NONE}

---
spring:
  config:
    activate:
      on-profile:
        - staging
        - prod
  datasource:
    batch:
      hikari:
        jdbc-url: jdbc:mysql://mypro:3306/batch?allowPublicKeyRetrieval=true&characterEncoding=UTF-8
        username: 
        password: 
        driver-class-name: com.mysql.cj.jdbc.Driver
        pool-name: 'read-and-write-pool'
  batch:
    jdbc:
      initialize-schema: never

---
spring:
  config:
    activate:
      on-profile: local
  datasource:
    batch:
      hikari:
        jdbc-url: jdbc:h2:mem:batch;MODE=MySQL;DB_CLOSE_ON_EXIT=FALSE
        username: sa
        password: password
        driver-class-name: org.h2.Driver
        pool-name: 'read-and-write-pool'
```


### Config

```java
@Configuration
@EnableConfigurationProperties(BatchProperties.class)
public class BatchConfig {

  // batchDatasource 사용을 위한 수동 빈 등록
  @Bean
  @ConditionalOnMissingBean
  @ConditionalOnProperty(prefix = "spring.batch.job", name = "enabled", havingValue = "true", matchIfMissing = true)
  public JobLauncherApplicationRunner jobLauncherApplicationRunner(JobLauncher jobLauncher, JobExplorer jobExplorer,
      JobRepository jobRepository, BatchProperties properties) {
    JobLauncherApplicationRunner runner = new JobLauncherApplicationRunner(jobLauncher, jobExplorer, jobRepository);
    String jobNames = properties.getJob().getName();
    if (StringUtils.hasText(jobNames)) {
      runner.setJobName(jobNames);
    }
    return runner;
  }

  // batchDatasource 사용을 위한 수동 빈 등록
  @Bean
  @ConditionalOnMissingBean(BatchDataSourceScriptDatabaseInitializer.class)
  BatchDataSourceScriptDatabaseInitializer batchDataSourceInitializer(DataSource dataSource,
      @BatchDataSource ObjectProvider<DataSource> batchDataSource, BatchProperties properties) {
    return new BatchDataSourceScriptDatabaseInitializer(batchDataSource.getIfAvailable(() -> dataSource),
        properties.getJdbc());
  }

  @BatchDataSource
  @ConfigurationProperties(prefix = "spring.datasource.batch.hikari")
  @Bean("batchDataSource")
  public DataSource batchDataSource() {
      return DataSourceBuilder.create().type(HikariDataSource.class).build();
  }

  @Bean
  public PlatformTransactionManager batchTransactionManager(
          @Qualifier("batchDataSource") DataSource batchDataSource) {
      return new DataSourceTransactionManager(batchDataSource);
  }
}
```

```java
// datasource, tm 커스텀 적용
@EnableBatchProcessing(dataSourceRef = "batchDataSource", transactionManagerRef = "batchTransactionManager")
@Import({BatchConfig.class})
public class BatchApplication {

    public static void main(String[] args) {
        SpringApplication springApplication =
                new SpringApplicationBuilder(BatchApplication.class).web(NONE).build();
        springApplication.run(args);
    }
}
```

### Job

```java
@Getter
@NoArgsConstructor
@JobScope
@Component
public class ExampleJobParameter {

    private LocalDate date;

    @Value("${chunk-size:1000}")
    private int chunkSize;

    @Value("#{jobParameters[date]}")
    public void setDate(String date) {
        this.date = LocalDate.parse(date, DateTimeFormatter.ISO_DATE);
    }
}
```

```java
@Configuration
public class ExampleJobConfig {

    public static final String JOB_NAME = "EXAMPLE_JOB";
    private final Step exampleStep;

    public ExampleJobConfig(
            @Qualifier(ExampleStepConfig.STEP_NAME) Step exampleStep) {
        this.exampleStep = exampleStep;
    }
    @Bean(JOB_NAME)
    public Job exampleJob(JobRepository jobRepository) {
        return new JobBuilder(JOB_NAME, jobRepository)
                .incrementer(new RunIdIncrementer())
                .start(ExampleJobConfig)
                .build();
    }
}
```

### Step

```java
@RequiredArgsConstructor
@Configuration
public class ExampleStepConfig {

    public static final String STEP_NAME = ExampleJobConfig.JOB_NAME + ".EXAMPLE_STEP";

    private final EntityManagerFactory entityManagerFactory;
    private final ExampleJobParameter jobParameter;

    @Bean(STEP_NAME)
    @JobScope
    public Step exampleStep(
            JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder(STEP_NAME, jobRepository)
                .<Customer, Customer>chunk(jobParameter.getChunkSize(), transactionManager)
                .reader(exampleItemReader())
                .writer(exampleItemWriter())
                .build();
    }

    @Bean
    @StepScope
    public ItemReader<User> exampleItemReader() {
      return new JpaPagingItemReaderBuilder<User>()
                .name("exampleItemReader")
                .entityManagerFactory(entityManagerFactory)
                .pageSize(jobParameter.getChunkSize())
                .queryString("SELECT u FROM User u")
                .build();
    }

    @Bean
    public ItemWriter<User> exampleItemWriter() {
        return users -> {
            for (User user : users) {
                System.out.println(user);
            }
        };
    }
}
```

- Job 실행 커멘드

```command
$ java -jar --job.name=EXAMPLE_JOB date=2023-06-01 --chunk_size=100
```

### Test

```java
@SpringBatchTest
@SpringBootTest(classes = {BatchTestConfig.class})
class ExampleJobConfigTest {

    @Autowired private JobLauncherTestUtils jobLauncherTestUtils;

    @Test
    public void exampleJobConfigTest(@Qualifier(ExampleJobConfig.JOB_NAME) Job job)
            throws Exception {
        // given
        jobLauncherTestUtils.setJob(job);
        final JobParameters jobParameters =
                jobLauncherTestUtils
                        .getUniqueJobParametersBuilder()
                        .addString("date", "2023-06-01")
                        .toJobParameters();

        // when
        final JobExecution jobExecution = jobLauncherTestUtils.launchJob(jobParameters);

        // then
        assertEquals(BatchStatus.COMPLETED, jobExecution.getStatus());
    }
}
```

[Spring Batch 5.0 Migration Guide](https://github.com/spring-projects/spring-batch/wiki/Spring-Batch-5.0-Migration-Guide)  
[What’s New in Spring Batch 5.0](https://docs.spring.io/spring-batch/docs/current/reference/html/whatsnew.html)