---
layout: post
title: 'Spring REST Docs'
categories: Spring
tags: [Spring]
---
REST API 문서를 자동화 하는 도구에는 `Spring REST Docs`와 `Swagger` 등이 있다.  
- `Spring REST Docs`의 장점 : 테스트가 성공해야 문서가 작성되며 실제 코드에는 추가되는 코드가 없다
- `AsciiDoc`의 형태로 문서가 작성된다

### build.gradle

```gradle
plugins { 
	id "org.asciidoctor.convert" version "1.5.9.2"
}

dependencies {
	asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor'
	testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc'
}

ext {           // 전역변수 설정
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
	copy {
		from "${asciidoctor.outputDir}"         // build/docs/asciidoc
		into 'src/main/resources/static/docs'
	}
}
```
- `test` → `asciidoctor` → `bootJar` 순으로 실행

- 작동순서

1. Controller 테스트를 실행하고 테스트에 성공하면 스니펫이 만들어진다. (`build/generated-snippets` 경로)
2. `src/docs/asciidoc/index.adoc` 베이스 아스키닥스 파일에서 만들어진 스니펫을 조합하여 문서를 작성한다.
3. 문서 작성을 완료하고 build하면 베이스 아스키닥스 파일을 기반으로 `build/docs/asciidoc/index.html`이 만들어 진다.
4. 추가 설정을 하면 build폴더에 생성된 `index.html`을 프로젝트의 static 폴더에도 복사 할 수 있다.  `src/main/resources/static/docs`
   - 이렇게 복사하면 서버에서 `http://localhost:8080/docs/index.html`를 통해 문서를 읽을 수 있다

### 설정
- `test.java.project.common.RestDocsConfig`
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


### Entity, Controller

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


### 1. Test를 통해 스니펫 생성
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


### 2. index.adoc 작성


### 3. 빌드 작성 및 index.html 복사
- gradle 기준 build나 bootJar를 하면 `index.adoc(1)`를 기반으로 `index.html(2)`이 생성된다
- 또한, 추가적인 설정을 통해(build.gradle 참조) 생성된 index.html을 프로젝트 내 static 폴더로 복사(3)를 하면 서버에서도 링크를 통해 확인이 가능하다
![image](https://user-images.githubusercontent.com/48157259/153324934-6bdc6448-85a1-45eb-a821-bed6de619c2d.png)
![image](https://user-images.githubusercontent.com/48157259/153325160-f168882e-1d14-4e1b-b01a-9517d4c1f16d.png)