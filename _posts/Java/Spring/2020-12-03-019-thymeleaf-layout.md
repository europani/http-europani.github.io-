---
layout: post
title: '[Thymeleaf] 타임리프 template layout'
categories: Spring
tags: [Spring, Thymeleaf]
---
공통 page를 `fragment`, `layout` 형식으로 조립할 수 있는 템플릿엔진  
`Thymeleaf 2`버전을 기준으로 작성하였다

```gradle
dependencies {
    ...
    implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
}
```

```html
<html xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
```

### Thymeleaf 2 버전 변경점
- 2버전 부터 3가지가 deprecated 되어 금지되어 졌다.
```
WARN 16864 --- [nio-8080-exec-1] n.n.u.t.decorators.DecoratorProcessor    : The layout:decorator/data-layout-decorator processor has been deprecated and will be removed in the next major version of the layout dialect.  Please use layout:decorate/data-layout-decorate instead to future-proof your code.  See https://github.com/ultraq/thymeleaf-layout-dialect/issues/95 for more information.
WARN 16864 --- [nio-8080-exec-1] n.n.u.t.expressions.ExpressionProcessor  : Fragment expression "layout/base" is being wrapped as a Thymeleaf 3 fragment expression (~{...}) for backwards compatibility purposes.  This wrapping will be dropped in the next major version of the expression processor, so please rewrite as a Thymeleaf 3 fragment expression to future-proof your code.  See https://github.com/thymeleaf/thymeleaf/issues/451 for more information.
WARN 16864 --- [nio-8080-exec-7] n.n.u.t.fragments.FragmentProcessor      : You don't need to put the layout:fragment/data-layout-fragment attribute into the <head> section - the decoration process will automatically copy the <head> section of your content templates into your layout page.
```

#### (1) layout:decorate <br> (2) Fragment Expression : ~{ }
`layout:decorator="layout/base` -> `layout:decorate="~{layout/base}"`

#### (3) <head> 태그
- Fragment를 head태그로 감싸 사용할 필요 없이 **각 `content의 <head>` 안에 적으면 자동으로 `layout의 <head>`로 들어간다**

<hr>

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
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
       
    <head th:fragment="headerFragment">
        <meta charset="UTF-8">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
    </head>
    
</html>
```

#### footer.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
      
    <footer th:fragment="footerFragment">
        copyright
    </footer>
    
</html>
```

### 2. layout
- 레이아웃 틀

- `<th:replace = "fragment경로 :: fragment이름">` : fragment 사용  
  - fragment 경로는 `templates` 하위폴더부터 시작한다.
- `<layout:fragment="이름">` : content에서 구현된 fragment 사용

#### ★ base.html
```html
<html lang="ko" 
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">

<head>
    <title>layout Page</title>           <!-- 랜더링시 content의 title 태그에 의해 덮어씌워짐 -->
</head>
<th:block th:replace="fragment/header :: headerFragment"></th:block>    <!-- 헤더 -->

<th:block layout:fragment="content"></th:block>                         <!-- 본문(변화하는 부분) -->

<th:block layout:fragment="javascript"></th:block>
<th:block th:replace="fragment/footer :: footerFragment"></th:block>    <!-- 푸터 -->

</html>
```
→ layout 페이지에 `<head>`태그가 존재하지 않으면 content 페이지의 <head> 내용이 증발한다


### 3. content (fragment에 해당)
- **본문 부분 : 내용이 페이지마다 변화하는 부분**
  
- `<layout:decorate = "~{layout경로}">` : fragment를 적용시킬 레이아웃
  - layout 경로는 `templates` 하위폴더부터 시작한다.
- `<th:block layout:fragment="이름">~~~</th:block>` : fragment 구현

#### content.html
```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">		       <!-- templates/layout/base.html -->
    
    <head>
        <title>Content Page</title>
        <link rel="stylesheet" href="/css/content.css">
    </head>  

    <th:block layout:fragment="content">
        <h1>content</h1>
    </th:block>

    <th:block layout:fragment="javascript">
        <script>
            console.log("script part");
        </script>
    </th:block>
</html>
```
→ <head>태그 내용이 layout의 <head> 태그 안으로 들어간다


- 랜더링 결과

```HTML
<!DOCTYPE html>
<html lang="ko"
      xmlns="http://www.w3.org/1999/xhtml">

<head>
    <title>Content Page</title>
    <link rel="stylesheet" href="/css/content.css">
    <meta charset="UTF-8">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
</head>
<body>
    content

    <script>
    console.log("script part");
    </script>

    <footer>
        copyright
    </footer>
</body>
</html>

```