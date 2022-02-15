---
layout: post
title: 'Spring HATEOAS'
categories: Spring
tags: [Spring, HATEOAS]
---
### HATEOAS란?
Hypermedia As The Engine Of Application State의 약자이며 **하이퍼미디어를 통해 정보를 동적으로 제공**할 수 있습니다  
API에서 리소스에 대해 어떤 행동을 할 수 있는지에 대해 하이퍼미디어(링크)를 제공하여 다른 상태로 전이가 가능합니다  
REST API의 구성요소 중 하나인데 HATEOAS를 만족해야 진정한 REST API라고 할 수 있습니다  

- Uniform Interface of REST API
  - 필요한 이유 : 서버와 클라이언트가 독립적으로 진화하면 서버의 기능이 변경되어도 클라이언트에서 변경을 할 필요가 없다
  - Self-Descriptive : 기능이 변경되어도 메시지는 언제나 해석이 가능해야 한다
  - HATEOAS : 어떤 상태에서 전이 가능한 링크를 제공해야 한다



#### Resource

Resource는 데이터와 링크로 구성되며 링크는 rel과 href로 구성됩니다
 
`Resource` = `Data` + `Link`  
\- Data : 서버에서 제공하는 데이터  
\- Link : 다른 상태로 전이 가능한 링크정보  

`Link` = `rel` + `href`  
\- rel : 링크 이름  
\- href : 링크 URL  


### build.gradle

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-hateoas'
}
```

### 단일 Resource
- `EntityModel` 사용

- `EntityModel.of(데이터)` : 해당 데이터를 Resource에 담아 return하는 스태틱 메서드
- `add(링크)` : Resource에 링크를 추가하는 메서드
- `linkTo(controller).slash(문자)` : 해당 controller의 method URI를 `controller/문자` 형태로 URI 반환
- `methodOn(controller)` : Method 이름을 사용하여 해당 method에 설정된 URI를 반환한다

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
        resource.add(linkTo(UserController.class).slash("mypage").withRel("My Page"));
        resource.add(Link.of("/docs/index.html#event").withRel("Event Docs"));           // Self-Descriptive를 위한 Docs링크

        return ResponseEntity.ok(resource);
    }
}
```

```json
{
    "id": 1,
    "title": "test title",
    "content": "test content",
    "_links": {
        "self": {
            "href": "http://localhost:8080/api/event/1"
        },
        "My Page": {
            "href": "http://localhost:8080/user/mypage"
        },
        "Event Docs": {
            "href": "/docs/index.html#event"
        }
    }
}
```

### 컬렉션 Resource
- `CollectionNodel` 사용

- `CollectionModel.of(컬렉션, 링크..)` : 해당 컬렉션 데이터를 Resource에 담아 return하는 스태틱 메서드

```java
@RequestMapping("/api/event")
@RequiredArgsConstructor
@RestController
public class EventController {

    private final EventService eventService;
    private final EventConverter eventConverter;

    @GetMapping
    public ResponseEntity getEventList() {
        List<EntityModel<Event>> eventList = eventService.getEventList()
                 .stream().map(event -> eventConverter.toModel(event)).collect(Collectors.toList());

        CollectionModel<EntityModel<Event>> resource = CollectionModel.of(eventList,
            linkTo(methodOn(this.getClass()).getEventList()).withSelfRel());
        resource.add(Link.of("/docs/index.html#event-list").withRel("Event List Docs"));           // Self-Descriptive를 위한 Docs링크

        return ResponseEntity.ok(resource);
    }
}
```

- 컬렉션에 들어있는 데이터를 resource로 변환하여 EntityModel로 만드는 과정을 캡슐화할 수 있다
  - `RepresentationModelAssembler<from, to>`

```java
@Component
public class EventConverter implements RepresentationModelAssembler<Event, EntityModel<Event>> {
    @Override
    public EntityModel<Event> toModel(Event event) {
        return EntityModel.of(event,
                linkTo(methodOn(EventController.class).getEvent(event.getId())).withSelfRel());
    }
}
```

```json
{
    "_embedded": {
        "eventList": [
            {
                "id": 1,
                "title": "test title",
                "content": "test content",
                "_links": {
                    "self": {
                        "href": "http://localhost:8080/api/event/1"
                    }
                }
            },
            {
                "id": 2,
                "title": "title",
                "content": "content",
                "_links": {
                    "self": {
                        "href": "http://localhost:8080/api/event/2"
                    }
                }
            }
        ]
    },
    "_links": {
        "self": {
            "href": "http://localhost:8080/support/event"
        },
        "Event List Docs": {
            "href": "/docs/index.html#event-list"
        }
    }
}
```

### Test

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

        mvc.perform(get("/api/event")
                        .contentType(MediaType.APPLICATION_JSON)
                        .accept(MediaTypes.HAL_JSON))
                .andExpect(status().isOk())
                .andExpect(header().string(HttpHeaders.CONTENT_TYPE, MediaTypes.HAL_JSON_VALUE))
                .andExpect(jsonPath("$..title").exists())
                .andExpect(jsonPath("$..content").exists())
                .andExpect(jsonPath("_links.self").exists())
                .andDo(print());
        verify(eventService).getEventList();
    }
}
```

[Spring HATEOAS 공식문헌](https://docs.spring.io/spring-hateoas/docs/current/reference/html/#reference)