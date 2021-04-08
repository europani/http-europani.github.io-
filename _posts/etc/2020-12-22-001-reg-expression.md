---
layout: post
title: 정규표현식(Regular Expression)
categories: etc
tags: [etc]
---

### 메타문자 : 특별한 용도로 사용되는 문자
. ^ $ * + ? { } [ ] \ | ( )

**- ^ : 문자의 시작을 나타냄**  
**- $ : 문자의 끝을 나타냄**  
**- r' ' : raw-string, 백슬래시(\\)를 문자로 인식** -> url패턴을 사용할 때 디렉토리 구분 표시(\\)를 편하게 나타낼수 있음

일반적으로 url을 나타낼 때 <span style="color:red;">**r'^url패턴$'**</span> 식으로 나타냄

### 문자클래스 [ ]
- 표현식에 문자를 넣을 수 있음

**[문자] : 1개만 찾음**  
ex) [abc] : a, b, c 중 하나만 찾음 (one of)

**[^문자] : not**  
ex) [^abc] : a, b, c를 제외한 하나만 찾음 (none of)

**\- : 범위**  
ex1) \[a-zA-Z\] : 알파벳   
ex2) \[0-9\] : 숫자  
ex3) \[a-e\] = \[abcde\]  

\\d : 숫자  (=\[0-9\])  
\\D : 숫자가 아닌것  (=\[^0-9\])  
\\s : 공백 (=\[ \\t\\n\\r\\f\\v\])  
\\S : 공백이 아닌것 (=\[^ \\t\\n\\r\\f\\v\])  
\\w : 문자 (=\[a-zA-Z0-9\_\])  
\\W : 문자가 아닌것 (=\[^a-zA-Z0-9\_\])  

\\t : tab  
\\v : vertical tab  
\\n : 줄바꿈

**. : 한문자 (공백포함 X)**  
ex) a.b : a와 b사이에 문자 하나

cf) [.]은 .(dot)을 의미함                 ex) a[.]b = a.b 텍스트

**| : or**  
ex) a|b : a이거나 b 여야함


### 반복

**+ : 1회 이상  = {1, }**  
ex) a+b : a가 1번이상 반복  
O : aab aaab aaaab  
X : ab (∵ a가 0번반복)

*** : 0회 이상 = {0, }**  
ex) a*b : a가 0번이상 반복  
O : ab aab aaab aaaab

**? : 0~1회 (있거나 없거나) = {0, 1}**  
ex) a?b : a가 있거나 없거나  
O : b ab

**{i} : i회**  
ex1) d{3} : 숫자 3개 필수  
ex2) ca{2}t : caat

**{m, n} : m~n회**  
\- m생략시 0, n생략시 무한대  
ex1) ca{2, 3}t : a가 2~3번 반복  
O : caat caaat   
X : cat (∵ a가 1번반복) caaaat (∵ a가 4번반복)

ex2) ca{2, }t : a가 2번이상 반복  
O : caat caaat caaaat  
X : cat(∵ a가 1번반복)


### 플래그(Flag)


[www.nextree.co.kr/p4327/](http://www.nextree.co.kr/p4327/)