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

#### header.html (fragment)
- `<layout:fragment="이름">` : fragment 선언

```html
<html xmlns:th="http://www.thymeleaf.org">
       
    <div layout:fragment="headerFragment">
        <h1>HEADER</h1>
    </div>
    
</html>
```

#### footer.html (fragment)

```html
<html xmlns:th="http://www.thymeleaf.org">
      
    <div layout:fragment="footerFragment">
        <h1>FOOTER</h1>
    </div>
    
</html>
```

#### ★ layout.html
- `<th:include = "fragment경로 :: fragment이름">` : fragment 사용  
- `<layout:fragment="이름">` : content에서 구현된 fragment 사용

```html
<html lagn="ko" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">

<head>
    <th:block th:include="fragment/header :: headerFragment"></th:block>
</head>
<body>
    <th:block layout:fragment="abc"></th:block>
    
    <th:block th:include="fragment/footer :: footerFragment"></th:block>
</body>
</html>
```

#### content.html
- `<layout:decorator = "layout경로&이름">` : fragment를 적용시킬 레이아웃  
- `<th:block layout:fragment="이름">~~~</th:block>`

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorator="layout">		<!-- layout.html -->
      
      <th:block layout:fragment="abc">
          <h1>content</h1>
      </th:block>
</html>
```