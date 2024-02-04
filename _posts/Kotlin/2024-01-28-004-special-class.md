---
layout: post
title: '[Kotlin] 특별 클래스(Data, Enum)'
categories: Kotlin
tags: [Kotlin]
---

### 1. Data Class
- 데이터를 가지고 있는 DTO 클래스
- `data` 키워드를 붙이면 `equals()`, `hashCode()`, `toString()` 을 자동으로 만들어 줌
- 생성자를 통해 `setter`, `getter` 자동 생성
- named parameter를 통해 `builer`까지 사용 가능
- JDK 16부터 도입된 `record`와 같음

```java
public class PersonDto {

  private final String name;
  private final int age;

  public PersonDto(String name, int age) {
    this.name = name;
    this.age = age;
  };

  // getter
  // equals, hashCode, toString
}
```

↓↓↓

```kotlin
data class personDto(
  val name: String,
  val age: Int
)
```

### 2. Enum class

```java
@Getter
@AllArgsConstructor
public enum Contury {
  KOREA("KO"),
  JAPAN("JP"),
  USA("US");

  private final String code;
}
```

↓↓↓

```kotlin
enum class Contury(
  private val code: String,
) {
  KOREA("KO"),
  JAPAN("JP"),
  USA("US");

  val isAsia get() = this.code == "KO" || "JP"
}
```
