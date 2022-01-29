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

Resource는 데이터와 링크로 구성되며 링크는 rel과 href로 구성됩니다
 
`Resource` = `Data` + `Link`  
\- Data : 서버에서 제공하는 데이터  
\- Link : 다른 상태로 전이 가능한 링크정보  

`Link` = `rel` + `href`  
\- rel : 링크 이름  
\- href : 링크 URL  

### 1. 설정

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-hateoas'
}
```

### 2. Controller

```java

```


### 3. Test

```java

```