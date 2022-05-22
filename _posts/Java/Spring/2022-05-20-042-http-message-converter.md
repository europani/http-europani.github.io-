---
layout: post
title: '[스프링] HttpMessageConverter'
categories: Spring
tags: [Spring]
---
- `HttpMessageConverter`는 HTTP Request나 Response의 내용을 다룰 때 사용한다
- Spring MVC에서 다음의 경우에는 `ViewResolver` 대신 `HttpMessageConverter`가 동작한다
  - Request : `@RequestBody`, `HttpEntity`, `RequestEntity`
  - Response : `@ResponseBody`, `HttpEntity`, `ResponseEntity`
- 메시지 컨버터는 **클래스타입**과 **미디어타입**을 체크해 적절한 컨버터를 호출한다

```java
public interface HttpMessageConverter<T> {
	boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);

	boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);

	T read(Class<? extends T> clazz, HttpInputMessage inputMessage)
			throws IOException, HttpMessageNotReadableException;

	void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException;
}
```

- `canRead()`, `canWrite()` : 메시지 컨버터가 해당 클래스, 미디어타입을 지원하는지 확인
- `read()`, `write()` : 메시지 컨버터를 통해 메시지를 읽고 쓰는 기능

### 스프링부트 기본 메시지 컨버터

```
0 = ByteArrayHttpMessageConverter
1 = StringHttpMessageConverter
2 = MappingJackson2HttpMessageConverter
...
```

1. `ByteArrayHttpMessageConverter`
   - 클래스타입 : `byte[]` / 미디어타입 : `*/*`
   - 요청 ex) @RequestBody byte[] data
   - 응답 ex) ResponseBody return byte[] 

2. `StringHttpMessageConverter`
   - 클래스타입 : `String` / 미디어타입 : `*/*`
   - 요청 ex) @RequestBody String data
   - 응답 ex) @ResponseBody return "ok" 

3. `MappingJackson2HttpMessageConverter`
   - 클래스타입 : `Object` or `HashMap` / 미디어타입 : `applcation/json`등
   - 요청 ex) @RequestBody HelloData data
   - 응답 ex) @ResponseBody return helloData 

#### HTTP 요청 읽어오기
- 우선순위 위에서부터 `canRead()`로 클래스타입과 미디어타입`Content-Type`이 일치해 해당 메시지 컨버터가 읽어 올 수 있는지 확인
- `false`일 경우 다음 우선순위로 넘어가서 다시 확인
- `true`일 경우 `read()` 호출해서 객체 생성 및 반환


#### HTTP 응답 생성
- 우선순위 위에서부터 `canWrite()`로 클래스타입과 미디어타입`Accept`이 일치해 해당 메시지 컨버터가 쓸 수 있는지 확인
- `false`일 경우 다음 우선순위로 넘어가서 다시 확인
- `true`일 경우 `write()` 호출해서 응답 메시지 바디에 데이터 생성



### 동작 방식
![image](https://user-images.githubusercontent.com/48157259/169652241-8724937b-2417-47fc-aa47-60280aba97d6.png)

- `HttpMessageConverter`는 스프링 MVC에서 @RequestMapping를 처리하는 **`RequestMappingHandlerAdapter`를 통해 동작**한다
  
#### ArgumentResolver
- `RequestMappingHandlerAdapter`는 `ArgumentResolver`를 호출하여 다양한 파라미터를 맵핑할 수 있다
  - `HandlerMethodArgumentResolver`를 그냥 `ArgumentResolver`라고 부른다
  - 스프링에는 이를 상속한 30개 이상의 `ArgumentResolver`가 구현되어 있다

[Spring Docs about ArgumentResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments)

```java
public interface HandlerMethodArgumentResolver {
   boolean supportsParameter(MethodParameter parameter);

   @Nullable
   Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
         NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;
}
```
- `supportsParameter` : 매개변수로 들어온 parameter를 지원되는지 체크하는 메서드
- `resolveArgument` : 체크에서 `true`가 나오면 실체 객체를 생성해주는 메서드

#### ReturnValueHandler
- 이 또한 ArgumentResolver가 요청값을 처리하는 것과 같이 응답 값을 변환하고 처리한다.
  - `HandlerMethodReturnValueHandler`를 그냥 `ReturnValueHandler`라고 부른다
  - 스프링에는 이를 상속한 10개 이상의 `ReturnValueHandler`가 구현되어 있다

[Spring Docs about ReturnValueHandler](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types)

```java
public interface HandlerMethodReturnValueHandler {
   boolean supportsReturnType(MethodParameter returnType);

   void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
         ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception;
}
```


#### Request
- `@RequestBody`나 `HttpEntity` 를 처리하는 `ArgumentResolver`들이 `HttpMessageConverter`를 사용해서 필요한 객체를 생성

#### Response
- `@ResponseBody`나 `HttpEntity` 를 처리하는 `ReturnValueHandler`들이 `HttpMessageConverter`를 사용해서 필요한 객체를 생성
