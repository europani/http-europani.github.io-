---
layout: post
title: '[스프링] 트랜잭션과 @Transactional 어노테이션'
categories: Spring
tags: [Spring, JDBC, AOP]
---
## 트랜잭션
- 트랜잭션이란 논리적 작업 단위를 의미한다
- 스프링에서는 트랜잭션을 직접 설정할 수 있다
- 트랜잭션을 시작한다는 의미는 **수동 커밋 모드**를 설정한다는 것을 의미 할 수 있다
  - 자동 커밋 모드는 SQL마다 커밋이 실행된다
- 사용자 요청마다 커넥션을 생성하면 커넥션은 세션을 만들고 세션은 트랜잭션을 시작한다
![](https://user-images.githubusercontent.com/48157259/172029619-f63e8514-1714-4f1b-8c89-5085ecc1c2de.png)

- `RuntimeException`, `Error` 발생시 rollback
- `CheckedException` 발생시 commit

### 트랜잭션 매니저
- 트랜잭션을 시작하고 결과에 따라 `commit` or `rollback`을 한다

```java
public interface PlatformTransactionManager extends TransactionManager {
   // 트랜잭션 시작
   TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;

   void commit(TransactionStatus status) throws TransactionException;

   void rollback(TransactionStatus status) throws TransactionException;
}
```

- 각 플랫폼은 `PlatformTransactionManager`를 구현한 구현체를 갖고 있다

![](https://user-images.githubusercontent.com/48157259/172029548-40068ee1-71e1-4c14-b5e8-2241d4b1fe34.png)

#### 트랜잭션 동기화 매니저
- 트랜잭션을 유지하기 위해서는 처음부터 끝까지 같은 커넥션을 사용해야 한다
- 트랜잭션이 시작된 커넥션을 보관하는 역할을 한다

```java
public abstract class TransactionSynchronizationManager {
   private static final ThreadLocal<Map<Object, Object>> resources = new NamedThreadLocal<>("Transactional resources");
   ...
   @Nullable
   public static Object getResource(Object key) {
      Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
      return doGetResource(actualKey);
   }
   ...
}
```

![](https://user-images.githubusercontent.com/48157259/172029867-eebbaba8-b1b9-44db-bfca-8ed0d4521c55.png)
1. 트랜잭션 매니저가 데이터소스를 통해 커넥션을 만들고 트랜잭션을 시작
2. 트랜잭션 매니저는 트랜잭션이 시작된 커넥션을 동기화 매니저에 보관
3. 리포지토리는 보관된 커낵션을 꺼내 사용
4. 트랜잭션이 종료되면 트랜잭션 메니저는 결과에 따라 `commit` or `rollback`을 하고 보관된 커넥션을 들고와 트랜잭션을 종료하고 커넥션을 종료


## @Transactional
`org.springframework.transaction.annotation`
- 스프링에서 선언적 트랜잭션을 설정하는 어노테이션
- **클래스**나 **메서드** 레벨에서 사용해서 트랜잭션을 설정할 수 있다
- AOP를 사용해 **프록시 패턴**으로 구현된다
- 프록시객체는 `PlatformTransactionManager`를 사용해 트랜잭션을 시작하고 결과에 따라 commit or rollback을 한다

![](https://user-images.githubusercontent.com/48157259/172030736-65835e07-5623-4ab2-bd19-c2aa50ba4c52.png)

- `public`에만 트랜잭션 적용
  - 스프링의 트랜잭션 AOP는 `public`에만 적용되도록 기본으로 설정되어 있다. 
  - `protected`, `default`, `private`에 `@Transactional`을 붙이면 그냥 무시된다
  - 왜냐하면 트랜잭션은 주로 비즈니스 로직의 시작점에 걸게 되는데 이 부분은 대부분 외부에 열어주는 포인트이다

### 트랜잭션 프록시 동작방식

```java
@Slf4j
@Service
static class BasicService {
   @Transactional
   public void tx() {
      log.info("call tx");
      boolean txActive = TransactionSynchronizationManager.isActualTransactionActive();
      log.info("tx active={}", txActive);    // true
   }

   public void nonTx() {
      log.info("call nonTx");
      boolean txActive = TransactionSynchronizationManager.isActualTransactionActive();
      log.info("tx active={}", txActive);    // false
   } 
}

@Slf4j
@SpringBootTest
public class TxBasicTest {
   @Autowired
   BasicService basicService;

   @Test
   void proxyCheck() {
      log.info("aop class={}", basicService.getClass());    // BasicService$$EnhancerBySpringCGLIB..
      assertThat(AopUtils.isAopProxy(basicService)).isTrue();
   }

   @Test
   void txTest() {
      basicService.tx();
      basicService.nonTx();
   }
```

![](https://user-images.githubusercontent.com/48157259/172994439-f65ae872-ea42-43cc-aa18-ae2569f44f18.png)
- `@Transcational`을 하나라도 포함하고 있는 클래스는 프록시를 생성해 스프링 컨테이너에 등록한다
- 클라이언트가 `@Transcational` 메서드를 호출했을 경우
  - 해당 메서드는 트랜잭션 대상이기 때문에 프록시는 트랜잭션을 시작하고[=공통 로직] 실제 객체의 메서드를 실행한다
  - 메서드 실행이 종료되면 다시 프록시로 돌아와 결과에 따라 `commit` 이나 `rollback`이 수행된다
- 클라이언트가 `@Transcational`이 아닌 메서드 호출했을 경우
  - 프록시는 트랜잭션을 시작하지 않고 바로 실제 객체의 메서드를 실행한다

### readOnly 속성
- 트랜잭션을 읽기전용으로 설정하여 데이터가 의도치않게 변경되는 것을 방지한다
- readOnly 트랜잭션이 시작된 이후 INSERT, UPDATE, DELETE가 실행되면 **예외 발생**
- 플랫폼별 성능 최적화 결과
  - `Hibernate` : `FlushMode.MANUAL`로 설정하여 **스냅샷을 기록하지 않아** 더티체킹을 생략해 성능상의 이점이 있다
  - `JdbcTemplate` : 변경 기능 실행시 예외발생
  - `JDBC Driver` : 변경 쿼리 발생시 예외발생 (DB와 드라이버 버전에 따라 다름)


### isolation 속성
- 동시에 여러 트랜잭션에 의해 변경 사항이 어떻게 적용되는 지를 설정
- 격리 수준 레벨이 올라갈 수록 성능 저하의 우려가 생긴다

#### 부작용
1. `Dirty Read` : 변경사항이 commit되지 않은 값을 다른 트랜잭션이 읽는 경우  
   ex) 트랜잭션A가 어떤 값을 1 -> 2로 변경하고 아직 커밋하지 않았을 때 트랜잭션B가 같은 값을 읽는 경우 2를 조회한다
2. `Non-Repeatable Read` : 한 트랜잭션 내에서 값을 조회할 때, 동시성 문제로 인하여 같은 쿼리를 실행했을 때 다른 결과를 반환하는 경우  
   ex) 트랜잭션A가 어떤 값 1을 조회했다. 이 때, 트랜잭션B가 그 값을 1 -> 2로 변경하고 커밋한 후 트랜잭션A가 다시 조회할 경우 2를 조회하게 된다
3. `Phantom Read` : 외부에서 수행하는 삽입/삭제로 인해 한 트랜잭션 내에서 같은 쿼리를 실행했을 때 다른 값을 반환하는 경우  

#### 1. DEFAULT
- 기본 값(DBMS의 isolation 레벨을 따름)

#### 2. READ_UNCOMMITTED (L0)
- 커밋되지 않은(트랜잭션이 진행중인) 데이터에 대한 읽기를 허용
- 모든 부작용 발생

#### 3. READ_COMMITTED (L1)
- 커밋된 데이터만 읽기 허용
- 즉, 어떤 데이터가 변경되는 중일때 해당 데이터에 다른 트랜잭션이 접근 할 수 없다 
- `Dirty Read` 방지

#### 4. REPEATABLE_READ (L2)
- 트랜잭션이 완료될 때까지 SELECT문이 사용하는 모든 데이터에 `shared lock`이 걸려 그 데이터의 수정이 불가하다
- `Dirty Read`, `Non-Repeatable Read` 방지

#### 5. SERIALIZABLE (L3)
- MVCC(동시 접근)을 막는다
  - MVCC : Multi-Version Coucurrency Control (다중버전 동시성 제어)
- 순차적으로 실행하는 것과 동일한 결과를 갖는다
- `Dirty Read`, `Non-Repeatable Read`, `Phantom Read` 방지


### propagation 속성
- 둘 이상의 트랜잭션을 사용할 때 트랜잭션의 범위를 설정

![](https://user-images.githubusercontent.com/48157259/173064328-7f9a62cf-a832-4b35-8ab0-2c4e6e4c7bf8.png)
- 논리 트랜잭션 : 트랜잭션 매니저를 통해 트랜잭션을 사용하는 단위
- 물리 트랜잭션 : 실제 DB에 적용되는 트랜잭션
- 물리 트랜잭션 안에 논리 트랜잭션을 포함시킨다
- 원칙
  - 모든 논리 트랜잭션이 `commit`되어야 물리 트랜잭션이 커밋된다
  - 하나의 논리 트랜잭션이라도 `rollback`되면 물리 트랜잭션도 롤백된다

#### 1. REQUIRED (기본값)
- 기존 트랜잭션이 있다면 그 트랜잭션에 참여해 하나의 트랜잭션처럼 만들어 준다  
  - 참여한다는 것은 추가된 내부 트랜잭션이 외부 트랜잭션을 그대로 이어 받아 따른다는 것이다
  - 1개의 물리 트랜잭션에 2개의 논리 트랜잭션이 존재(하나의 물리 트랜잭션으로 묶임)
- 기존 트랜잭션이 없다면 새로운 트랜잭션을 생성한다

```java
@Test
void inner_commit() {
   log.info("외부 트랜잭션 시작");
   TransactionStatus outer = txManager.getTransaction(new DefaultTransactionAttribute());
   log.info("outer.isNewTransaction()={}", outer.isNewTransaction());  // true

   log.info("내부 트랜잭션 시작");
   TransactionStatus inner = txManager.getTransaction(new DefaultTransactionAttribute());
   log.info("inner.isNewTransaction()={}", inner.isNewTransaction());  // false
   
   log.info("내부 트랜잭션 커밋");
   txManager.commit(inner);
   log.info("외부 트랜잭션 커밋");
   txManager.commit(outer);
}
```

![](https://user-images.githubusercontent.com/48157259/173066154-acec9185-1384-476f-bb74-f4d673e31a76.png)
![](https://user-images.githubusercontent.com/48157259/173067136-61164117-42ed-4b1c-88dd-ad7dc58687f9.png)
1. `getTransaction()`으로 **외부 트랜잭션 시작**
2. 트랜잭센 매니저는 데이터소스를 통해 커넥션 생성
3. 수동 커밋모드로 설정하고 **물리 트랜잭션 시작**
4. 트랜잭션 메니저는 커넥션을 트랜잭션 동기화 매니저에 보관
5. `TransactionStatus`에 트랜잭션 생성결과를 담아서 반환
6. 비즈니스 로직 실행
7. 비즈니스 로직 내부에서 `getTransaction()`으로 **내부 트랜잭션 시작**
8. 트랜잭션 매니저는 트랜잭션 동기화 매니저를 통해 기존 트랜잭션 존재여부 확인
9. 이미 존재한다면 기존 트랜잭션을 사용
10. `TransactionStatus`에 트랜잭션 생성결과를 담아서 반환
11. 나머지 비즈니스 로직 수행

12. 내부 트랜잭션 내의 로직 종료 후 커밋
13. 내부 트랜잭션은 신규 트랜잭션이 아니기 때문에 commit을 호출하지 않고 **내부 트랜잭션 종료**
14. 외부 트랜잭션 로직 종료 후 커밋
15. 외부 트랜잭션은 신규 트랜잭션이기에 `commit` 실행 후 **외부 트랜잭션 종료**
16. **물리 트랜잭션 종료** 

- 내부 트랜잭션 롤백시
  - 내부 트랜잭션은 신규 트랜잭션이 아니기 때문에 물리 트랜잭션을 건드릴 수 없다
  - 트랜잭션 동기화 매니저에 `rollbackOnly=true`로 표시해 외부 트랜잭션에서 확인 할수 있도록 한다

![](https://user-images.githubusercontent.com/48157259/173069266-a53cd42f-06d9-4dff-9fb1-e2e0ec6dd8b6.png)

#### 2. REQUIRES_NEW
- 항상 새로운 트랜잭션을 생성한다
- 기존 트랜잭션이 있다면 잠시 보류시킨다
  
#### 3. SUPPORT
- 기존 트랜잭션이 없다면 트랜잭션 없이 로직을 수행한다
- 기존 트랜잭션이 있다면 기존 트랜잭션에 참여한다

#### 4. NOT_SUPPORT
- 트랜잭션을 사용하지 않는다
- 기존 트랜잭션이 있다면 잠시 보류시킨다
  
#### 5. MANDATORY
- 트랜잭션이 반드시 존재해야 한다
- 기존 트랜잭션이 없다면 예외 발생 `IllegalTransactionStateException`
- 기존 트랜잭션이 있다면 기존 트랜잭션에 참여한다

#### 6. NEVER
- 트랜잭션을 사용하지 않는다
- 기존 트랜잭션이 있다면 예외 발생 `IllegalTransactionStateException`

#### 7. NESTED
- 기존 트랜잭션이 없다면 새로운 트랜잭션을 생성한다
- 기존 트랜잭션이 있다면 그 트랜잭션에 중첩해서 트랜잭션을 생성한다