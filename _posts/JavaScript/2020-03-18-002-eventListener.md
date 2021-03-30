---
layout: post
title: 이벤트 리스너(EventHandler(Listener))
categories: Javascript
tags: [Javascript]
---

● addEventListener : 이벤트가 발생하면 작동하는 함수 (이벤트리스너 = 이벤트핸들러)

→ 변수.addEventListener('이벤트', 이벤트핸들러); \- 이벤트 : click, resize 등등..

● removeEventListener : 만들어져있는 이벤트를 제거하는 함수

 → 변수.removeEventListener('이벤트', 이벤트핸들러);

```javascript
const v = document.querySelector("#title");
function handClick() {
    this.style.color = "red"
} 

v.addEventListener("click", handClick);
```

submit을 사용할때는 v에 form이 들어가야함.

\- JavaScript로 이벤트 부여

```javascript
<script>

let h1 = document.querySelector('h1');
function whenClick() {
    alert('CLICK');
} 

// 1. onclick 속성 사용
h1.onclick = whenClick;
document.querySelector('h1').onclick = function () {	// 변수를 사용하지 않는 방법
    alert('CLICK');
}
// 2. addEventListener 사용
h1.addEventListener('click', whenClick);
h1.addEventListener('click', function () {		// 변수를 사용하지 않는 방법
    alert('CLICK');
});

</script>
```

\- HTML 태그의 속성으로 직접 이벤트 부여

```
<body>
   <h1 onclick="alert('누름')">click</h1>
</body>
```

● event : 이벤트핸들러의 파라미터에 이벤트에 대한 정보가 담겨있는 객체를 받음.

```
let h1 = document.querySelector('h1')

h1.addEventListener("click", function (event) {
	console.log(event.target)
});
```

event객체의 target 속성에는 event가 일어난 객체가 담겨있음. (event.target = this)

● event.preventDefault() : 이벤트가 일어나지 않게 방지함

  event.stopPropagation() : 이벤트가 상위 엘리먼트로의 전파를 방지

```
function handleName(event) {
    event.preventDefault();
	....
}
```

● Event Bubbling : 이벤트가 발생했을 때 해당 이벤트가 하위->상위(window) 화면 요소들로 전달되어 가는 특성

    Event Capturing :이벤트가 발생했을 때 해당 이벤트가 상위(window)->하위 화면 요소들로 전달되어 가는 특성

즉, 하위에서 이벤트가 발생하면 해당 태그의 이벤트가 일어나고 상위의 부모 태그의 이벤트들도 순차적으로 일어남.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FctErmH%2FbtqF1BUCcFB%2FJY1RdW9YmK7pveLaC2k6DK%2Fimg.png)

\- Bubbling을 막는 법 : event.cancelBubble = true;