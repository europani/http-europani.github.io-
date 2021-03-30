---
layout: post
title: 콜백 함수
categories: Javascript
tags: [Javascript]
---

함수의 매개변수에 함수를 넣어서 함수 안에서 어떤 특정한 시점에 그함수를 실행시킴

콜백 함수에 익명 함수를 넣을 수도 있음.

```javascript
// 함수 선언문(익명함수로 구현)
function callFunctionTenTimes(otherFunction) {
    for (let i = 0; i < 10; i++) {
         otherFunction();
    }
}

callFunctionTenTimes( function () {
    document.write('Hello World..!' + '<br>');
    }
)
```

```javascript
// 함수 표현식
function callTenTimes(otherFunction) {
    for (let i = 0; i < 10; i++) {
        otherFunction();
    }
}

let callback = function () {
    document.write('함수호출 ');
}

callTenTimes(callback);
```