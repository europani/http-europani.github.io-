---
layout: post
title: typeof, instanceof 연산자
categories: Javascript
tags: [Javascript]
---

● typeof 객체 : 객체의 타입을 출력

```javascript
console.log(typeof 'string');	// string
console.log(typeof 1);		// number
console.log(typeof []);		// object
console.log(typeof {});		// object
console.log(typeof null);	// object
console.log(typeof function() { });		// function
```

● instanceof 객체 : 객체의 인스턴스타입을 출력

```javascript
let Person = function(){ 
    this.name = "Chris";
}; 
let inst = new Person(); 

inst instanceof Person;     // true
inst instanceof Object;     // true
typeof inst;      	  // object
```