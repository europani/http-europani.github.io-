---
layout: post
title: IntelliJ(인텔리제이) 단축키
categories: Java
tags: [Java, IntelliJ]
---
<span style="color:blue">파란글씨</span>는 윈도우와 Mac 사이에 명령어 차이가 클 경우이다  
일반적으로 아래와 같이 상응한다

|윈도우|Mac|
|:--:|:--:|  
|Ctrl|⌘ (or ⌃)|
|Alt|⌥|
|Shift|⇧|

#### 편집&이동

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Ctrl + D|⌘ + D|한줄 복사|
|Ctrl + Y|<span style="color:blue">⌘ + ⌫</span>|한줄 삭제|
|Ctrl + G|??|입력한 라인으로 이동|
|Alt + Shift + ↑↓|⌥ + ⇧ + ↑↓|라인 이동|
|Ctrl + Shift + ↑↓|⌘ + ⇧ + ↑↓|구문 안 라인 이동|
|Ctrl + Shift + []|⌘ + ⇧ + []|이전/이후 탭 이동|
|Ctrl + Alt + ←→|⌘ + ⌥ + ←→|이전/이후 작업위치로 이동|
|||
|Shift + Enter|⇧ + Enter|현재 커서에서 한줄 밑에서 시작|
|Ctrl + Shift + Enter|??|현재 커서에서 한줄 위에서 시작|
|??|⌘ + ⇧ + Enter|; 완성|

#### 검색

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Ctrl + F|⌘ + F|해당 파일에서 글자 검색|
|Ctrl + Shift + F|⌘ + ⇧ + F|전체에서 글자 검색|
|Ctrl + R|⌘ + R|해당 파일에서 글자 검색후 변경|
|Ctrl + Shift + R|⌘ + ⇧ + R|전체에서 글자 검색후 변경|
|Double Shift|(일치)|파일 검색|

#### 기능

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Ctrl + H|<span style="color:blue">⌃ + H</span>|계층 구조|
|Ctrl + F12|⌘ + F12|파일 구조|
|Alt + F12|⌥ + F12|터미널|
|Alt + Insert|<span style="color:blue">⌘ + N</span>|Source 메뉴(getter/setter 등)|
|Ctrl + Alt + Insert|<span style="color:blue">⌘ + N</span>|새파일 생성|
|Ctrl + Alt + S|<span style="color:blue">⌘ + ,</span>|환경설정|
|??|<span style="color:blue">⌘ + ;</span>|프로젝트 설정|


#### <span style="color:red">유용한 기능</span>

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|F2|(일치)|오류로 이동|
|Ctrl + I|span style="color:blue">⌃ + I</span>|Override 메서드 자동완성|
|Ctrl + O|span style="color:blue">⌃ + O</span>|Implement 메서드 자동완성|
|Ctrl + P|⌘ + P|파라미터 정보|
|Ctrl + Q|<span style="color:blue">F1</span>|퀵 document|
|Alt + Enter|⌥ + Enter|퀵 fix|
|Ctrl + Shift + I|<span style="color:blue">⌥ + Space</span>|퀵 definition|
|Ctrl + Space|<span style="color:blue">⌃ + Space</span>|자동완성|
|Ctrl + Shift + Space|<span style="color:blue">⌃ + ⇧ + Space</span>|스마트 자동완성|
|Ctrl + Alt + T|⌘ + ⌥ + T|선택한 코드 감싸기(if/while 등)|
|Ctrl + Alt + L|⌘ + ⌥ + L|문서전체 자동정렬|
|Ctrl + Alt + O|⌘ + ⌥ + O|import 정리|
|Alt + F7|⌥ + F7|해당 변수 사용위치 검색|
|Ctrl + Alt + F7|⌘ + ⌥ + F7|해당 변수 사용위치로 이동|
|Ctrl + B|⌘ + B|선언부로 이동(상위)|
|Ctrl + Alt + B|⌘ + ⌥ + B|구현부로 이동(하위)|

#### <span style="color:red">리팩토링</span>

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Shift + F6|⇧ + F6|이름 일괄변경|
|Ctrl + Shift + F6|⌘ + ⇧ + F6|타입 일괄변경|
|Ctrl + Alt + C|⌘ + ⌥ + C|extract Constant|
|Ctrl + Alt + F|⌘ + ⌥ + F|extract Field|
|Ctrl + Alt + V|⌘ + ⌥ + V|extract Variable|
|Ctrl + Alt + P|⌘ + ⌥ + P|extract Parameter|
|Ctrl + Alt + M|⌘ + ⌥ + M|extract Method|
|???|⌘ + ⌥ + N|inline|
|Ctrl + Shift + Alt + T|<span style="color:blue">⌥ + T</span>|리펙토링 메뉴|

#### 빌드&실행

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Ctrl + F9|⌘ + F9|빌드|
|Shift + F9|<span style="color:blue">⌃ + D</span>|디버그|
|Shift + F10|<span style="color:blue">⌃ + R</span>|실행|
|??|<span style="color:blue">⌃ + ⇧ + R</span>|테스트 코드 실행|


#### 테스트

|윈도우|Mac|설명|
|:--:|:--:|:--:|
|Ctrl + Shift + T|⌘ + ⇧ + T|테스트 작성|