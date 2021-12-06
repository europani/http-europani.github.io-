---
layout: post
title: 'HttpEntity, ResponseEntity'
categories: Spring
tags: [Spring]
---
스프링 프레임워크에는 `HTTP`의 요청과 응답의 `Header`, `Body`를 포함하는 클래스가 존재한다

#### 1. HttpEntity
- HTTP의 `header`, `body`를 담을 수 있다

```java
public class HttpEntity<T> {

	private final HttpHeaders headers;
	@Nullable
	private final T body;

    ...
    public HttpEntity(T body) {
		this(body, null);
	}
    public HttpEntity(@Nullable T body, @Nullable MultiValueMap<String, String> headers) {
		this.body = body;
		this.headers = HttpHeaders.readOnlyHttpHeaders(headers != null ? headers : new HttpHeaders());
	}
    ...
}
```

#### 2. ResponseEntity
- `HttpEntity` + `응답코드` 로 구성

```java
public class ResponseEntity<T> extends HttpEntity<T> {

	private final Object status;

    ...
    public ResponseEntity(@Nullable T body, HttpStatus status) {
		this(body, null, status);
	}
    
    public ResponseEntity(@Nullable T body, @Nullable MultiValueMap<String, String> headers, HttpStatus status) {
		this(body, headers, (Object) status);
	}

    private ResponseEntity(@Nullable T body, @Nullable MultiValueMap<String, String> headers, Object status) {
		super(body, headers);
		Assert.notNull(status, "HttpStatus must not be null");
		this.status = status;
	}
    ...
}
```

### 사용 예시(Controller)

```java
@RestController
@AllArgsConstructor
public class ResponseBodyController {
    private ResponseEntityService service;

    @GetMapping("/response-body-string-v1")
    public ResponseEntity<String> responseBodyV1() {
        return new ResponseEntity<>("ok", HttpStatus.OK);
    }

    @GetMapping("/response-body-string-v2")
    public String responseBodyV2() {
        return "ok";
    }

    @GetMapping("/response-body-json-v1")
    public ResponseEntity<HelloData> responseBodyJsonV1() {
        HelloData helloData = new HelloData();
        helloData.setUsername("catsbi");
        helloData.setAge(20);

        return new ResponseEntity<>(helloData, HttpStatus.OK);
    }

    @ResponseStatus(HttpStatus.OK)
    @GetMapping("/response-body-json-v2")
    public HelloData responseBodyJsonV2() {
        HelloData helloData = new HelloData();
        helloData.setUsername("catsbi");
        helloData.setAge(20);

        return helloData;
    }

}

```

