---
layout: post
title: 'Spring REST Docs'
categories: Spring
tags: [Spring]
---
REST API 문서를 자동화 하는 도구에는 `Spring REST Docs`와 `Swagger` 등이 있다.  
- df`Spring REST Docs`의 장점 : 테스트가 성공해야 문서가 작성되며 실제 코드에는 추가되는 코드가 없다
- `AsciiDoc`의 형태로 문서가 작성된다

### 1. 설정

```gradle
plugins { 
	id "org.asciidoctor.convert" version "1.5.9.2"
}

dependencies {
	asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor'
	testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc'
}

ext { 
	snippetsDir = file('build/generated-snippets')    // 해당 폴더에 문서생성
}

test {
	outputs.dir snippetsDir
}

asciidoctor {
	dependsOn test
	inputs.dir snippetsDir
}

bootJar {   // JAR파일에 문서를 같이 빌드하기 위한 설정
	dependsOn asciidoctor
	from ("$/html5") {
		into 'static/docs'
	}
}
```
`test` → `asciidoctor` → `bootJar` 순으로 실행


### 2. Entity, Controller

```java
@Entity
public class Event {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;


}
```

```java
@RequestMapping("/event")
@RequiredArgsConstructor
@RestController
public class EventController {

    private final EventService eventService;




}
```


### 3. Test

```java
@AutoConfigureRestDocs(uriScheme = "https", uriHost = "docs.api.com")
@WebMvcTest(EventController.class)
public class EventControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private MemberService memberService;
}
```