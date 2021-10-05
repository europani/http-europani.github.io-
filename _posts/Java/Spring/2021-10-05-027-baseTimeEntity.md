---
layout: post
title: 'BaseTimeEntity'
categories: Spring
tags: [Spring]
---
모든 Entity의 상위 클래스에서 createdDate, updateDate를 자동으로 관리해주는 역할

- `Date` 자료형을 지양하고 8버전에 추가된 `LocalDate`, `LocalDateTime`을 사용하는 것을 추천한다
- `BaseTimeEntity` 추상클래스를 구현하고 `Entity` 클래스들에게 상속 시켜 사용한다

```java
@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)  // Auditing 기능 포함
public abstract class BaseTimeEntity {

    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}

```
- `@CreatedDate` : 생성시 날짜 자동 생성
- `@LastModifiedDate` : 수정시 날짜 자동 갱신

```java
public class Members extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 20, nullable = false)
    private String username;

    @Column(nullable = false)
    private String password;

    @Column(length = 10)
    private String name;

    @Enumerated(EnumType.STRING)
    private MembersRole role;
}
```

- 메인클래스에서 `JPA Auditing`을 활성화 시켜야 한다

```java
@EnableJpaAuditing // JPA Auditing 활성화
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```