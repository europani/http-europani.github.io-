---
layout: post
title: '필터(Filter)와 인터셉터(Interceptor)'
categories: Spring
tags: [Spring]
---
- 공통으로 관심이 있는 부분을 공통 관심사라고 한다.   
- 예를 들면 웹 서비스에 로그인이라는 기능이 있는데 이를 공통 관심사라고 할 수 있다.  
- 일반적으로 공통 관심사는 AOP로 처리할 수 있는데 웹과 관련된 공통 관심사는 `서블릿 필터`나 `스프링 인터셉터`로 처리할 수 있다. 
- 웹에서는 HTTP 헤더나 URL 정보가 필요한데 필터와 인터셉터에서 `HttpServletRequest`를 제공해 주기 때문에 편리하게 처리할 수 있다.

## 필터(Filter)
`javax.servlet.filter`
- 필터는 **서블릿**에서 제공한다 
- 스프링 컨테이너가 아닌 톰캣같은 서블릿 컨테이너(=웹 컨테이너)에 의해 관리된다
- Spring MVC에서 프론트 컨트롤러인 `DispatcherServlet`으로 요청이 호출되기 전에 동작한다

![image](https://user-images.githubusercontent.com/48157259/168003827-9771917b-d4c6-46ca-b6fe-9413d68c1e7f.png)

### 필터 메소드

```java
public interface Filter {
    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
```

1. `init()` : 서블릿 컨테이너 생성 시 필터 객체를 초기화하는 메서드
2. `doFilter()` : request시 필터 실행
3. `destory()` : 서블릿 컨테이너 종료 시 필터 객체를 제거하는 메서드


## 인터셉터(Interceptor)
`org.springframework.web.servlet.HandlerInterceptor`
- 인터셉터는 **스프링**에서 제공한다. 
- 스프링 컨테이너에서 관리한다
- Spring MVC에서 프론트 컨트롤러인 `DispatcherServlet`으로 요청이 호출되기 후에 동작한다
- DispatcherServlet이 `HandlerMapping`을 통해 적절한 핸들러(=컨트롤러)를 찾는데 인터셉터가 찾은 컨트롤러에 관여한다면 동작한다
- 단, 인터셉터가 동작하는 시기를 설정할 수 있다

![image](https://user-images.githubusercontent.com/48157259/168004911-1cc5afe6-cb7a-4753-81b8-f0f808eb0200.png)

### 인터셉터 메서드

```java
public interface HandlerInterceptor {
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		return true;
	}

	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}

	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}

}
```

1. `preHandler()` : 컨트롤러가 실행되기 전에 동작하는 메서드, 반환값이 false면 그 뒤는 진행되지 않는다.
2. `postHandler()` : 컨트롤러가 실행된 후에 동작하는 메서드
3. `afterCompletion()` : 뷰가 렌더링 되어 모든 작업이 완료 된 후에 동작하는 메서드


## 필터와 인터셉터 사용용도

|구분|용도|
|:--:|:--:|
|필터|공통 보안작업(보안공격), 모든 요청의 감시, 인코딩 등|
|인터셉터|세부 보안작업(인가), API 호출의 감시, Controller로 넘겨주는 데이터 가공 등|