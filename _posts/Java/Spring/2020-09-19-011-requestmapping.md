---
layout: post
title: '@RequestMapping, @GetMapping, @PostMapping, @DeleteMapping, @PutMapping, @PatchMapping'
categories: Spring
tags: [Spring]
---

기존의 맵핑 방법인 @RequestMapping을 보완하여 스프링 4.3버전부터 @GetMapping, @PostMapping, @DeleteMapping, @PutMapping, @PatchMapping 등이 추가됨

● 기존의 맵핑 방식 (@RequestMapping)

```java
@RequestMapping("/memJoin")		// get방식
@RequestMapping(value="/memJoin" method="RequestMethod.GET")
public String Test(HttpServletRequest request, HttpServletResponse response) {
   ....
}

@RequestMapping(value="/memJoin" method="RequestMethod.POST") 	// post방식(delete, put, patch)
public String Test2(HttpServletRequest request, HttpServletResponse response) {
   ....
}

```

● 추가된 맵핑 방식 (@GetMapping, @PostMapping, @DeleteMapping, @PutMapping, @PatchMapping)

```java
@GetMapping(value="/memJoin")		// get방식
public String Test(HttpServletRequest request, HttpServletResponse response) {
   ....
}

@PostMapping(value="/memJoin")		// post방식
public String Test2(HttpServletRequest request, HttpServletResponse response) {
   ....
}

@DeleteMapping(value="/memJoin")		// delete방식
public String Test2(HttpServletRequest request, HttpServletResponse response) {
   ....
}

@PutMapping(value="/memJoin")		// put방식
public String Test2(HttpServletRequest request, HttpServletResponse response) {
   ....
}

@PatchMapping(value="/memJoin")		// patch방식
public String Test2(HttpServletRequest request, HttpServletResponse response) {
   ....
}

```