---
layout: post
title: (표준 객체) Array 객체
categories: Javascript
tags: [Javascript]
---

● Array.sort() : 정렬, 배열 요소를 모두 문자열로 보고 정렬

```
let array = [52, 273, 103, 32];
array.sort(function (left, right) {	// 오름차순
    return left - right;
});
```

● Array.slice(start, end) : 배열을 원하는부분만 복사해옴 (start <= x < end)

●Array.remove(index) : 배열의 index에 해당하는 내용 제거, Javascript에서는 요소를 remove하면 뒤에 있던 수들이 앞으로 땡겨지면서 index가 변경됨 -> 뒤에서부터 제거해야함