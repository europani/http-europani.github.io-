---
layout: post
title: (표준 객체) Object 객체
categories: Javascript
tags: [Javascript]
---

● Object.toString() : 객체를 문자열로 변환

```
let student = {
    name : '김자바',
    grade : 1,
    toString : function () {	// student객체를 문자열로 나타나게 정의
        return this.name + ' : ' + this.grade + '<br>';
    }
}
document.write(student);	
```

● Object.keys(객체) : 객체의 키들을 배열로 반환