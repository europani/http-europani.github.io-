---
layout: post
title: 'LocalDate, LocalTime, LocalDateTime, YearMonth 클래스'
categories: Java
tags: [Java]
---

- 8버전부터 날짜와 시간을 다루는 클래스인 `LocalDate`, `LocalTime`, `LocalDateTime`이 추가되었다 (java.time)
- `Date`와 `Calendar` 클래스 사용을 지양하자 (java.util)

### 1. LocalDate
- 날짜만 사용할 사용

```java
LocalDate today = LocalDate.now();              // 현재 날짜 출력 (2021-10-05)
LocalDate date = LocalDate.of(2021, 10, 04);    // 특정 날짜 출력 (2021-10-04)

date.getYear();         // 연도 : 2021
date.getMonth();        // 월 : OCTOBER
date.getMonthValue();   // 월 : 10
date.getDateOfMonth();  // 일 : 4
date.getDayOfYear();    // 올해 며칠 쨰 : 277
date.getDayOfWeek();    // 요일 : MONDAY
date.isLeapYear();      // 운년여부 : false

// LocalDate -> String
date.format(DateTimeFormatter.ofPattern("yyyy-MM-dd"));

// String -> LocalDate
LocalDate.parse("2021-10-05");

// LocalDate -> LocalDateTime
date.atTime(2, 30);
```


### 2. LocalTime
- 시간만 사용할 사용

```java
LocalTime now = LocalTime.now();                // 현재 시간 출력 (15:13:41)
LocalTime time = LocalTime.of(12, 32, 52, 22);  // 특정 시간 출력 (12:32:52.0000022)

time.getHour();     // 시 : 12
time.getMinute();   // 분 : 32
time.getSecond();   // 초 : 52
time.getNano();     // 밀리초 : 22

```


### 3. LocalDateTime
- 날짜와 시간 모두 사용할 사용

```java
LocalDateTime now = LocalDateTime.now();                        // 2021-10-05T15:13:41
LocalDateTime time = LocalDateTime.of(2021, 10, 05, 12, 32);    // 2021-10-05T12:32:00
// LocalDate + LocalTime 조합
LocalDateTime time2 = LocalDatetime.of(LocalDate.now(), LocalTime.now());

// LocalDateTime -> String
time.format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

// String -> LocalDateTime
LocalDateTime.parse("2021-10-05T12:32:52");
LocalDateTime.parse("2021-10-05 12:32:52", DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

// LocalDateTime -> LocalDate
LocalDate.from(time);
```

### 날짜 계산
#### 날짜 덧셈, 뺄셈
- `plus@@@()`, `minus@@@()` 메서드
  
```java
LocalDateTime now = LocalDateTime.now();

now.plusYears(1);    // 년
now.plusMonths(1);   // 달
now.plusWeeks(1);    // 주
now.plusDays(1);     // 일
now.plusHours(1);    // 시
now.plusMinutes(1);  // 분
now.plusSeconds(1);  // 초
now.plusNanos(1);    // 밀리초
```

#### 날짜 비교
- `isBefore()` `isAfter()` `isEqual()` 메서드
  
```java
LocalDateTime now = LocalDateTime.now();
LocalDateTime target = LocalDateTime.of(2020, 10, 05, 12, 32);

now.isBefore(target);   // false
now.isAfter(target);   // true
now.isEqual(target);   // false
```

#### 날짜 차이 계산
- `Period` 클래스

```java
LocalDate now = LocalDate.now();
LocalDate target = LocalDate.of(2021, 09, 05);

Period period = Period.between(now, target);

period.getYears();      // 0년
period.getMonths();     // 1개월
period.getDays();       // 0일
```

- 시간 기준으로 계산하기 (ChronoUnit)

```java
LocalDate now = LocalDate.now();
LocalDate target = LocalDate.of(2021, 09, 05);

ChronoUnit.DAYS.between(now, target);   // 30일
```

#### 시간 차이 계산
- `Duration` 클래스

```java
LocalTime now = LocalTime.now();  // 17:14:55
LocalTime target = LocalTime.of(18,17,35);

Duration duration = Duration.between(now, target);

duration.getSeconds();      
duration.getNano();
```

### 4. YearMonth
- 년과 월만 사용할 경우 사용할 수 있는 클래스 (yyyy-MM)
  
```java
YearMonth date = YearMonth.of(2021, 10);     // 2021-10

date.getYear();             // 2021
date.getMonth();            // OCTOBER
date.getMonthValue());      // 10
date.atEndOfMonth();        // 2021-10-31
date.lengthOfMonth();       // 31
date.plusMonth(1);          // 2021-11
date.minusMonth(1);         // 2021-09
```

- 한달 기준으로 검색하기

```java
public String requestList(YearMonth date, Model model) {
    LocalDateTime start = LocalDateTime.of(date.getYear(), date.getMonth(), 1, 0, 0);
    LocalDateTime end = LocalDateTime.of(date.atEndOfMonth(), LocalTime.of(23, 59, 59));

    List<RequestDTO> requests = requestService.getList(start, end);

    return "request/requestList";
}
```