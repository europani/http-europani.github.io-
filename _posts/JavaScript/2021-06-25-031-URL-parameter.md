---
layout: post
title: 'Javascript URL 파라미터 가져오기'
categories: Javascript
tags: [Javascript]
---

```
http://localhost:8080/notice?id=1
```
다음과 같은 URL에서 파라미터 부분의 값을 가져오고 싶을 때가 있다.

- URL 가져오기
  
```Javascript
let url = document.location.href;
console.log(url);       // http://localhost:8080/notice?id=1
```

- URL Query부분 가져오기
 
```Javascript
let query = window.location.search;
console.log(query);       // ?id=1
```

- URL Query의 파라미터 부분 가져오기

```Javascript
let query = window.location.search;
let param = new URLSearchParams(query);
let id = param.get('id');

console.log(id);        // 1
```