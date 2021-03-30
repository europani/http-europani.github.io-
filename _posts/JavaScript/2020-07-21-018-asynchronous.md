---
layout: post
title: Javascript 비동기 함수
categories: Javascript
tags: [Javascript]
---

call stack이 비워진 후(함수의 실행이 끝난 후)에야 실행되는 함수 -> 모든 함수가 실행 된 뒤에 실행됨

비동기 함수는 호출 시 event queue(task queue)에 대기하고 있다가 call stack이 비워지면 call stack으로 이동,실행

ex) setTimeout

```javascript
alert('a');  // 1
setTimeout(function () {
	alert('b'); // 3
}, 0)
alert('c');  // 2
```

\-> Top-Down 방식에 의해 a가 실행후 setTimeout이 호출되어 콜백함수를 event queue에 대기시킨 다음 c가 실행됨 그 후 call stack이 비워진 후에 event queue로부터 대기하고 있던 콜백함수를 실행시켜 b가 출력됨

\-> 타이머가 설정되어 있을 경우에는 설정한 시간이 지난 후에 event queue에 적재됨.

● 종류

**1\. setTimeout : 정해놓은 시간 후 함수를 한번 실행**

setTimeout("함수", 시간);        ex) setTimeout("getTime()", 100);

시간단위(ms)

**2\. setInterval : 정해놓은 시간을 주기로 함수를 반복 실행**

setInterval("함수", 시간);         ex) setInterval("getTime()", 100);