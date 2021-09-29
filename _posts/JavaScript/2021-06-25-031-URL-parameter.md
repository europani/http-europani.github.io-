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

#### URLSearchParams 객체
- URL의 쿼리 문자열에 대한 작업을 할수 있는 메서드 제공

1. `get(parameter)` : 해당 파라미터의 value 출력, 없을시 null 출력
2. `has(parameter)` : 해당 파라미터가 존재하면 true
3. `append(parameter, value)` : 파라미터 추가, 이미 파라미터가 존재해도 계속 추가된다
4. `set(parameter, value)` : 파라미터 수정
5. `delete(parameter)` : 파라미터 제거
6. `toString()` : 쿼리부분 출력, `?`는 제거되어 출력 된다

```javascript
let query = window.location.search;         // http://localhost:8080/notice?id=1&name=하나
let param = new URLSearchParams(query);     // ?id=1&name=하나

param.get("id");         // 1
param.getAll("id");      // [1]
param.has("id");         // true

param.append("writer", "유로파니");
param.toString();       // id=1&name=하나&writer=유로파니

param.set("writer", "europani");
param.toString();       // id=1&name=하나&writer=europani

param.delete("writer");
param.toString();       // id=1&name=하나
```

#### URL 파라미터를 이용한 필터 구현

```html
<html>
<div class="btnGroup" id="areaBtn">
    <button type="button">전체</button>
    <button type="button" data-id=1>서울</button>
    <button type="button" data-id=2>대전</button>
    <button type="button" data-id=3>대구</button>
    <button type="button" data-id=4>부산</button>
</div>
<div class="btnGroup" id="fieldBtn">
    <button type="button">전체</button>
    <button type="button" data-id=1>한식</button>
    <button type="button" data-id=2>중식</button>
    <button type="button" data-id=3>일식</button>
    <button type="button" data-id=4>양식</button>
    <button type="button" data-id=5>패스트푸드</button>
</div>
</html>
```

```javascript
let query = window.location.search;         // ?a=1&f=4 (서울, 양식)
let param = new URLSearchParams(query);
let area = param.get('a');      // 1
let field = param.get('f');     // 4

let allButton = document.querySelectorAll(".btnGroup button");
allButton.forEach(function (b) {
    b.addEventListener('click', filterButton);
});

function filterButton(e) {
    let type = e.target.parentNode.id;
    let id = e.target.getAttribute('data-id');

    if (type == 'areaBtn') {        // area 필터
        if (id != null) {
            param.set('a', id);
        } else {
            param.delete('a')
        }
    } else {                        // field 필터
        if (id != null) {
            param.set('f', id);
        } else {
            param.delete('f')
        }
    }

    location.href = "?" + param.toString();     // 필터적용 된 URL로 이동
}
```