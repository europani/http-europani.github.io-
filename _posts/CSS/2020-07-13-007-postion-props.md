---
layout: post
title: CSS 위치 속성(position)
categories: CSS
tags: [CSS]
---

● 상대 위치

static : 상->하 & 좌->우방향으로 순서대로 \[default\]

 - top, bottom, left, right 속성의 영향 X

relative : 초기(top-0px, left-0px) 위치 기준

● 절대 위치 : 뷰포인트(화면) 기준으로 위치 설정

absolute : 기준점에 따라 위치 \[기준점이 없을 때 defalut: top-0px, left-0px\]

 -> 기준점 : 상위 요소중에 static이 아닌 position

fixed : 화면을 기준으로 절대위치에 고정, 스크롤을 해도 따라다니며 고정되어 있음

```HTML
<!DOCTYPE html>
<html>
<head>
    <title>CSS3 Property Basic</title>
    <style>
    	body > div {    /* 부모 */
    		width: 400px; height: 100px;
    		border: 3px solid black;
    		position: relative;
    		overflow: auto;
    	}
        .box {          /* 자식 */
            width: 100px; height: 100px; 
            position: absolute;
        }
        .box:nth-child(1) {
            background-color: red;
            left: 10px; top: 10px;

            z-index: 100;
        }
        .box:nth-child(2) {
            background-color: green;
            left: 50px; top: 50px;

            z-index: 10;
        }
        .box:nth-child(3) { 
            background-color: blue;
            left: 90px; top: 90px;

            z-index: 500;
        }
    </style>
</head>
<body>
    <h1>Lorem ipsum dolor amet</h1>
    <div>
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
    </div>
    <h1>Lorem ipsum dolor amet</h1>
</body>
</html>

```

● overflow : 내용이 요소 크기를 벗어날 때, float과 많이쓰임

 - hidden : 벗어난 부분은 잘라 없어짐

 - scroll : 벗어난 부분을 스크롤로 내리면 볼 수 있음

 - auto : 내용이 크기를 벗어나면 자동으로 scroll적용

●float (left, right)

 - 위로 뜸(z축 방향), float를 이용해 하나를 위로 띄워서 좌우 배치를 할수 있음.

    clear를 사용하여 float되지 않아야 할 것들을 제자리에 놓음 ex) clear : left;