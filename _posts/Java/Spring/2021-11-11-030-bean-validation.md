---
layout: post
title: Bean Validation 유효성 검사
categories: Spring
tags: [Spring]
---

### Bean Validation 이란?  
Bean Validation 2.0 (JSR-380)이라는 기술 표준으로 검증 어노테이션를 통해 유효성을 검사 할 수 있는 인터페이스  
가장 많이 쓰는 구현체는 `hibernate Validator`이다. (ORM의 hibernate와는 무관하다)

### 0. 설정

```gradle
implementation 'org.springframework.boot:spring-boot-starter-validation'
```


### 1. 도메인

```java


```

- `@NotNull` : null 허용 안함
- `@NotEmpty` : null과 공백값("")을 허용 안함
- `@NotBlank` : null과 빈값(" ")을 허용 안함
- `@Max` : 최대 값 설정
- `@Range` : 범위 값 설정
- `@Email` : 이메일형식

- `message` 속성 : 에러 메시지를 커스텀할 수 있다

### 2. Controller
- `@Validated` 가 설정된 객체의 유효성을 검증하여 오류 발생 시 FieldError, ObjectError를 생성하여 BindingResult에 추가된다

```java

```

- `@Validated` : 해당 객체를 검증하겠다고 설정  
    (`@Valid`를 포함하며 groups 기능이 추가되어 있다. 다만, groups 기능을 잘 사용하지 않는다)
- `BindingResult` 클래스 : 오류가 발생 할 경우 오류 내용을 담는 객체  
  <span style="color:red">(반드시 @Validated 객체 뒤에 와야 한다)</span>


### 3. View

```html

```