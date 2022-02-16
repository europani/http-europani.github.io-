---
layout: post
title: 'Spring REST Docs'
categories: Spring
tags: [Spring, 'Rest Docs']
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
        EntityModel<Event> resource = EntityModel.of(event);
        resource.add(linkTo(methodOn(this.getClass()).getEvent(id)).withSelfRel());
        return ResponseEntity.ok(resource);
    }

    @GetMapping
    public ResponseEntity getEventList() {
        List<EntityModel<Event>> eventList = eventService.getEventList()
                 .stream().map(event -> eventConverter.toModel(event)).collect(Collectors.toList());

        CollectionModel<EntityModel<Event>> resource = CollectionModel.of(eventList,
            linkTo(methodOn(this.getClass()).getEventList()).withSelfRel());

        return ResponseEntity.ok(resource);
    }
}
```


### 1. Test를 통해 스니펫 생성
- `@AutoConfigureRestDocs` : Rest Docs를 사용하기 위한 어노테이션
- `andDo(document(스니펫명, 스니펫조각...)` : 스니펫명(identifier)을 지정하고 해당 스니펫에 속할 스니펫 조각들을 생성

```java
@AutoConfigureRestDocs(uriScheme = "https", uriHost = "docs.api.com")
@WebMvcTest(EventController.class)
@Import(RestDocsConfig.class)
public class EventControllerTest {

    @Autowired
    private MockMvc mvc;

    @Autowired
    ObjectMapper objectMapper;

    @MockBean
    private EventService eventService;

    @Test
    @DisplayName("이벤트 1건 조회")
    public void getEvent() throw Exception {
        // given
        Event event = Event.builder()
                .id(1L)
                .title("test")
                .content("test content")
                .build();
        given(eventService.getEvent(any())).willReturn(event);

        // when & then
        mvc.perform(get("/api/event/{id}", event.getId())
                .contentType(MediaType.APPLICATION_JSON)
                .accept(MediaTypes.HAL_JSON))
                // .content(objectMapper.writeValueAsString(id)))       // Request Body 필요없음
            .andExpect(status().isOk())
            .andExpect(header().string(HttpHeaders.CONTENT_TYPE, MediaTypes.HAL_JSON_VALUE))
            .andExpect(jsonPath("$.id").value(1L))
            .andExpect(jsonPath("$.title").exists())
            .andExpect(jsonPath("_links.self").exists())
            .andDo(print())
            .andDo(document("get-event",
                    links(
                        linkWithRel("self").description("link to self")
                    ),
                    requestHeaders(
                        headerWithName(HttpHeaders.ACCEPT).description("accept header"),
                        headerWithName(HttpHeaders.CONTENT_TYPE).description("content type")
                    ),
                    responseHeaders(
                        headerWithName(HttpHeaders.CONTENT_TYPE).description("content type")
                    ),
                    responseFields(
                        fieldWithPath("id").description("user id number"),
                        fieldWithPath("title").description("event title"),
                        fieldWithPath("content").description("event content"),
                        fieldWithPath("_links.self.href").description("link to self"))
                ));  
        verify(eventService).getEvent(any());
    }

    @Test
    @DisplayName("이벤트 List 조회")
    void getEventList() throws Exception {
        // given
        List<EventDTO> eventList = new ArrayList<>();

        for (long i=1L; i < 3L; i++) {
            EventDTO event = EventDTO.builder()
                    .id(i)
                    .title("event title " + i)
                    .content("event content " + i)
                    .build();
            eventList.add(event);
        }
        given(eventService.getEventList()).willReturn(eventList);

        // when & then
        mvc.perform(get("/api/event")
                        .contentType(MediaType.APPLICATION_JSON)
                        .accept(MediaTypes.HAL_JSON))
                .andExpect(status().isOk())
                .andExpect(header().string(HttpHeaders.CONTENT_TYPE, MediaTypes.HAL_JSON_VALUE))
                .andExpect(jsonPath("$..title").exists())
                .andExpect(jsonPath("$..content").exists())
                .andExpect(jsonPath("_links.self").exists())
                .andDo(print())
                .andDo(document("get-eventList",
                        links(
                            linkWithRel("self").description("link to self")
                        ),
                        requestHeaders(
                            headerWithName(HttpHeaders.ACCEPT).description("accept header"),
                            headerWithName(HttpHeaders.CONTENT_TYPE).description("content type")
                        ),
                        responseHeaders(
                            headerWithName(HttpHeaders.CONTENT_TYPE).description("content type")
                        ),
                        responseFields(
                            fieldWithPath("_embedded.eventList[].id").description("event id number"),
                            fieldWithPath("_embedded.eventList[].title").description("event title"),
                            fieldWithPath("_embedded.eventList[].content").description("event content"),
                            fieldWithPath("_embedded.eventList[]._links.self.href").description("link to self"),
                            fieldWithPath("_links.self.href").description("link to self")
                        )
                ));
        verify(eventService).getEventList();
    }
}
```

#### 스니펫 조각
1\. links - 하이퍼미디어
- Hateoa를 사용할 때 하이퍼미디어와 관련된 스니펫 조각을 생성 
- `linkWithRel(링크명).description(설명)` : 해당 링크의 이름과 설명을 설정

```java
links(
    linkWithRel("self").description("link to self")
)
```

2\. RequestHeaders, ResponseHeaders - Http 헤더
- Http 헤더의 스니펫 조각을 생성
- `headerWithName(헤더명).description(설명)` : 해당 헤더의 이름과 설명을 설정
  
```java
requestHeaders(
    headerWithName(HttpHeaders.ACCEPT).description("accept header"),
    headerWithName(HttpHeaders.CONTENT_TYPE).description("content type")
),
```

3\. RequestFields, ResponseFields - Http 바디 필드
- Http 바디 필드의 스니펫 조각을 생성
- `fieldWithPath(필드명).description(설명)` : 해당 필드의 이름과 설명을 설정

```java
responseFields(
    fieldWithPath("id").description("event id number"),
    fieldWithPath("title").description("event title"),
    fieldWithPath("content").description("event content"),
    fieldWithPath("_links.self.href").description("link to self"))
),
```

### 2. index.adoc 작성


### 3. 빌드 작성 및 index.html 복사
- gradle 기준 build나 bootJar를 하면 `index.adoc(1)`를 기반으로 `index.html(2)`이 생성된다
- 또한, 추가적인 설정을 통해(build.gradle 참조) 생성된 index.html을 프로젝트 내 static 폴더로 복사(3)를 하면 서버에서도 링크를 통해 확인이 가능하다
![image](https://user-images.githubusercontent.com/48157259/153324934-6bdc6448-85a1-45eb-a821-bed6de619c2d.png)
![image](https://user-images.githubusercontent.com/48157259/153325160-f168882e-1d14-4e1b-b01a-9517d4c1f16d.png)


[Spring Rest Docs 공식문헌](https://docs.spring.io/spring-restdocs/docs/current/reference/html5/)