---
layout: post
title: 'Spring Enum Validation'
categories: Spring
tags: [Spring]
---

- Enum을 파라미터로 받을 때 존재하지 않을 경우 `HttpMessageNotReadableException`처럼 컨버팅 exeception이 발생한다
- 이를 해결하고 적절한 에러로 전환하기 위해 컨버터를 사용해서 해결 할 수 있다

```java
public enum Platform {
    GOOGLE, NAVER, KAKAO;
}
```

- 컨버터 생성
  - 컨버터에서 커스텀 exception으로 에러전환을 할 수 있다

```java
public class PlatformConverter implements Converter<String, Platform> {
    @Override
    public Platform convert(final String platform) {
        try {
            return Platform.valueOf(platform.toUpperCase());
        } catch (IllegalArgumentException e) {
            throw new WrongPlatformException(platform);     // 에러 전환
        }
    }
}
```

- 컨버터 등록

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(final FormatterRegistry registry) {
        registry.addConverter(new PlatformConverter());
    }
}
```

```java
@RestController
@RequiredArgsConstructor
public class AuthController {

    private final AuthService authSerice;

    public void signup(final Platform platform, final String id, final String password) {
        authSerice.signup(platform, id, password);   
    }
}
```
