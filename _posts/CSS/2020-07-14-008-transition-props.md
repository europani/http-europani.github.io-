---
layout: post
title: CSS 전환 속성(transition)
categories: CSS
tags: [CSS]
---

정해진 시간동안 부드럽게 변화가 일어남

transition

transition-property : 전환 효과

transition-duration : 전환이 지속될 시간

transition-timing-function : 전환효과의 시간당 속도

transition-delay : 전환이 일어나기 전까지의 시간

```HTML
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
<style type="text/css">
	.box {
		width: 100px;	height: 100px;
		background-color: orange;
		
		-ms-transition-duration : 2s;		/* IE */
		-moz-transition-duration : 2s;		/* 파이어폭스 */
		-o-transition-duration : 2s;		/* 오페라 */
		-webkit-transition-duration : 2s;	/* 크롬, 사파리 */
		
		
	}
	.box:hover{
		width: 200px;	height: 200px;
	}
	.box:active {
		background-color: red;
	}
</style>
</head>
<body>
	<div class="box"></div>
</body>
</html>
```