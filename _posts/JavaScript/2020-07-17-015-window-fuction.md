---
layout: post
title: (표준 객체) Window 객체
categories: Javascript
tags: [Javascript]
---

JavaScript의 최상위 객체

window 객체는 전역객체이므로 도트연산자 없이 함수 호출가능.

● eval() : 문자열을 자바스크립트 코드로 변환해 실행

```javascript
let willEval = '';
willEval += 'let number = 10;';
willEval += 'document.write(number);';

eval(willEval);
```

● isFinity, isNaN : 유한이면, 숫자가 아니면 true

```javascript
let number1 = 10/0;     // 무한
let number2 = 10/'A';   // NaN

document.write(number1 + " : " + number2);			// Infinity : NaN
document.write(isFinite(number1)  + " : " + isNaN(number2));	// false : true
```

 단, -10/0 의 경우는 Infinity가 아니라고 나옴.

 NaN은 비교 불가 (NaN == NaN) -> 반드시 isNaN() 사용

● parseInt(), parseFloat()

 - parseInt('ff', 16) = 255

 - parseInt('10', 8) = 8

 - parseInt('10', 2) = 2

### 타이머함수

● setTimeout(function, millisecond) : 정해놓은 시간 후 함수를 한번 실행

● setInterval(function, millisecond) : 정해놓은 시간을 주기로 함수를 반복 실행

● clearTimeout(함수명 or 변수명) : 일정 시간 후 함수를 한번 실행하는 것을 중지

● clearInterval(함수명 or 변수명) : 일정 시간마다 함수를 반복하는 것을 중지

```javascript
let intervalID = setInterval(() => {
   document.write('Hello');
}, 1000);

setTimeout(() => {
    clearInterval(intervalID);
}, 10000);
```