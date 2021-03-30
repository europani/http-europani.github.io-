---
layout: post
title: arrow function(화살표 함수, 람다식)
categories: Javascript
tags: [Javascript]
---

```javascript
function getName(name) {
   return "Kim " + name ;
}

//위 함수는 아래 arrow함수와 같다.(함수표현식)
var getName = (name) => "Kim " + name;
```