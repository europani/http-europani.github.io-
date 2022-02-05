---
layout: post
title: 'Spring REST Docs'
categories: Spring
tags: [Spring]
---
REST API 문서를 자동화 하는 도구에는 `Spring REST Docs`와 `Swagger` 등이 있다.  
- `Spring REST Docs`의 장점 : 테스트가 성공해야 문서가 작성되며 실제 코드에는 추가되는 코드가 없다
- `AsciiDoc`의 형태로 문서가 작성된다

### 1. 설정

1\. `build.gradle`

```gradle
plugins { 
	id "org.asciidoctor.convert" version "1.5.9.2"
}

dependencies {
	asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor'
	testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc'
}

ext { 
	snippetsDir = file('build/generated-snippets')    // 해당 폴더에 스니펫 문서생성
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
- `test` → `asciidoctor` → `bootJar` 순으로 실행

2\. `test.java.project.common.RestDocsConfig`
- 출력 형태가 가독성 좋게 출력되도록 설정
- Test 클래스에서 `@Import`해서 설정을 사용

```java
@TestConfiguration
public class RestDocsConfig {

    @Bean
    public RestDocsMockMvcConfigurationCustomizer restDocsMockMvcConfigurationCustomizer() {
        return configurer -> configurer.operationPreprocessors()
                .withRequestDefaults(prettyPrint())
                .withResponseDefaults(prettyPrint());
    }
}
```


### 2. Entity, Controller

```java
@Entity
public class Event {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

	private String title;
	private String content;

}
```

```java
@RequestMapping("/api/event")
@RequiredArgsConstructor
@RestController
public class EventController {

    private final EventService eventService;


	@GetMapping("/{id}")
	public ResponseEntity getEvent(@PathVariable Long id) {
		Event event = eventService.getEvent(id);
		return ResponseEntity.ok(event);
	}
}
```


### 3. Test
- `@AutoConfigureRestDocs` : Rest Docs를 사용하기 위한 어노테이션

```java
@AutoConfigureRestDocs(uriScheme = "https", uriHost = "docs.api.com")
@WebMvcTest(EventController.class)
@Import(RestDocsConfig.class)
public class EventControllerTest {

    @Autowired
    private MockMvc mockMvc;

	@Autowired
    ObjectMapper objectMapper;

    @MockBean
    private EventService eventService;

	@Test
    @DisplayName("이벤트 1건 조회")
    public void getEventTest() throw Exception {
        // given
        Event event = Event.builder()
                .id(1L)
                .title("test")
				.content("test content")
                .build();

        // when & then
        mvc.perform(get("/api/event/{id}", event.getId())
                .contentType(MediaType.APPLICATION_JSON)
                // .content(objectMapper.writeValueAsString(id)))       // Request Body 필요없음
            .andExpect(status().isOk())
            .andExpect(header().string(HttpHeaders.CONTENT_TYPE))
            .andExpect(jsonPath("$.id").value(1L))
            .andExpect(jsonPath("$.title").exists())
            .andDo(print());
    }
}
```