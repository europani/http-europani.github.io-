---
layout: post
title: '@Transactional 어노테이션'
categories: Spring
tags: [Spring]
---
- 트랜잭션이란 논리적 작업 단위를 의미한다
- 스프링에서는 트랜잭션을 직접 설정할 수 있다

## @Transactional
- 스프링에서 선언적 트랜잭션을 설정하는 어노테이션
- **클래스**나 **메서드** 레벨에서 사용해서 트랜잭션을 설정할 수 있다
- AOP를 사용해 **프록시 패턴**으로 구현된다
- 프록시객체는 `PlatformTransactionManager`를 사용해 트랜잭션을 시작하고 결과에 따라 commit or rollback을 한다

```java
public interface PlatformTransactionManager extends TransactionManager {
   TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;

   void commit(TransactionStatus status) throws TransactionException;

   void rollback(TransactionStatus status) throws TransactionException;
}
```

### ReadOnly
- 트랜잭션을 읽기전용으로 설정하여 데이터가 의도치않게 변경되는 것을 방지한다
- readonly 트랜잭션이 시작된 이후 INSERT, UPDATE, DELETE가 실행되면 **예외 발생**
- hibernate를 사용할 경우 `FlushMode.MANUAL`로 설정하여 **스냅샷을 기록하지 않아** 더티체킹을 생략해 성능상의 이점이 있다


### Isolation
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


### Propagation
- 트랜잭션의 범위를 설정

#### 1. REQUIRED
- 기본 값
- 현재 활성화된 트랜잭션이 있다면 그 트랜잭션에 추가한다
- 현재 활성화된 트랜잭션이 없다면 새로운 트랜잭션을 생성한다

#### 2. SUPPORTS
- 현재 활성화된 트랜잭션이 있다면 해당 트랜잭션을 사용한다
- 현재 활성화된 트랜잭션이 없다면 트랜잭션 없이 로직을 수행한다

#### 3. MANDATORY
- 현재 활성화된 트랜잭션이 있다면 해당 트랜잭션을 사용한다
- 현재 활성화된 트랜잭션이 없다면 예외 발생

#### 4. REQUIRES_NEW
- 항상 새로운 트랜잭션을 생성한다
- 하지만 이미 진행중인 트랜잭션이 존재한다면 진행중인 트랜잭션을 잠시 보류시킨다

#### 5. NOT_SUPPORTED
- 트랜잭션을 사용하지 않는다
- 하지만 이미 진행중인 트랜잭션이 존재한다면 진행중인 트랜잭션을 잠시 보류시킨다

#### 6. NEVER
- 트랜잭션을 사용하지 않는다
- 하지만 이미 진행중인 트랜잭션이 존재한다면 예외 발생

#### 7. NESTED
- 현재 활성화된 트랜잭션이 있다면 그 트랜잭션에 중첩해서 트랜잭션을 생성한다
- 현재 활성화된 트랜잭션이 없다면 REQUIRED처럼 동작한다
