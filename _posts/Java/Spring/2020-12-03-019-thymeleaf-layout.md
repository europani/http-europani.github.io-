---
layout: post
title: '[Thymeleaf] 타임리프 template layout'
categories: Spring
tags: [Spring, Thymeleaf]
---

공통 page를 fragment, layout 형식으로 조립할 수 있는 템플릿엔진

```html
<html xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
```

fragment -> layout -> content 페이지 순으로 단계적으로 적용한다고 생각

#### header.html (fragment)

 - <th:fragment="이름"> : fragment 선언

```html
<html xmlns:th="http://www.thymeleaf.org">
       
    <!--headerFragment 선언-->
    <div th:fragment="headerFragment">
        <h1>HEADER</h1>
    </div>
    
</html>
```

#### footer.html (fragment)

```html
<html xmlns:th="http://www.thymeleaf.org">
      
    <!--footerFragment 선언-->
    <div th:fragment="footerFragment">
        <h1>FOOTER</h1>
    </div>
    
</html>
```

#### layout.html (layout) : fragment 적용 (th:replace 사용)

\- \<th:replace = "fragment경로 :: fragment이름"> : fragment 사용

\- \<layout:fragment="이름"> : content에서 구현된 fragment 사용 -> 레이아웃에 자리를 잡아놓는다고 생각

```html
<html lagn="ko" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">

<head>
    <th:block th:replace="fragment/header :: headerFragment"></th:block>
</head>
<body>
    <th:block layout:fragment="abc"></th:block>		<!-- content에서 구현된 abc 사용 -->
    
    <th:block th:replace="fragment/footer :: footerFragment"></th:block>
</body>
</html>
```

#### content.html (본문) : layout 적용 (layout:decorator 사용)

 - \<layout:decorator = "layout경로&이름">

 \- \<th:block layout:fragment="이름"> ........ </th:block> : 구현할 내용을 태그 안쪽에 적어서 fragment 구현

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorator="layout">		<!-- layout.html -->
      
      <th:block layout:fragment="abc">		<!-- abc 구현 -->
          <h1>content</h1>
      </th:block>
</html>
```