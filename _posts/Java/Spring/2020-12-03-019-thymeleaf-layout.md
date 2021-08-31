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

### 프로젝트 경로
```
├──java
├──resource
    ├── static (css, js)
    └── templates
        ├── fragment
        ├── layout
        └── content
```

### 1. fragment
- `<th:fragment="이름">` : fragment 선언
  
#### header.html
```html
<html xmlns:th="http://www.thymeleaf.org">
       
    <div th:fragment="headerFragment">
        <h1>HEADER</h1>
    </div>
    
</html>
```

#### footer.html
```html
<html xmlns:th="http://www.thymeleaf.org">
      
    <div th:fragment="footerFragment">
        <h1>FOOTER</h1>
    </div>
    
</html>
```

### 2. layout
- 레이아웃 틀

- `<th:replace = "fragment경로 :: fragment이름">` : fragment 사용  
  - fragment 경로는 `templates` 하위폴더부터 시작한다.
- `<layout:fragment="이름">` : content에서 구현된 fragment 사용

#### ★ base.html
```html
<html lagn="ko" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">

<head>
    <th:block th:replace="fragment/header :: headerFragment"></th:block>    <!-- 헤더 -->
</head>
<body>
    <th:block layout:fragment="content"></th:block>                         <!-- 본문(변화하는 부분) -->
    
    <th:block th:replace="fragment/footer :: footerFragment"></th:block>    <!-- 푸터 -->
</body>
</html>
```

### 3. content (fragment에 해당)
- **본문 부분 : 내용이 페이지마다 변화하는 부분**
  
- `<layout:decorator = "layout경로">` : fragment를 적용시킬 레이아웃  
- `<th:block layout:fragment="이름">~~~</th:block>`

#### content.html
```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorator="layout/base">		       <!-- base.html -->
      
      <th:block layout:fragment="content">
          <h1>content</h1>
      </th:block>
</html>
```