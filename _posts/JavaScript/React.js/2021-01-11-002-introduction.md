---
layout: post
title: React.js의 특징
categories: React.js
tags: [React.js]
---

React는 컴포넌트로 구성

컴포넌트는 클래스 또는 함수로 정의함

클래스를 정의하려면 **React.Component**를 상속받아야 함

### JSX(JavaScript XML)

\- JavaScript언어의 확장. JavaScript에서 HTML형식을 그대로 사용할 수 있게 하는 등 XML과 같은 문법을 native JavaScript로 변환해줌.

\- JavaScript 안에서 사용하다 보니 기존의 예약어 때문에 같은 기능의 명령어도 차이가 생김

1\. 모든 JSX는 container element에 감싸져야함.

```javascript
render() {
    return(
    <div>
        <h1>Hello</h1>
    </div>
    )
}
```

2\. JSX안에 JS를 표현할때는 { } 를 사용함

3\. 기존 HTML의 주석 <!-- --> 과는 다르게 { /\* \*/ }를 사용 (중괄호에 감싸서 사용)

 - container element 안에서만 주석을 사용할 수 있음

4\. className 사용 

```html
<div className="selected"></div>
       |
      ↓ (HTML에서는 이렇게 변환됨)
<div class="selected"></div>
```

5\. onClick 카멜표기법

```html
<button className="btn btn-primary" onClick={ (event) => this.sendMsg(event) }>
```

```html
<div id='root'><div>
```

```javascript
class App extends React.Component {
    render() {
        return (
            <div>hello React!!</div>
        );
    }
}

ReactDOM.render(<App/>, document.getElementById('root'));
```

→ ReactDOM.render(랜더링할코드, 랜더링할위치) : JSX 형태의 코드를 렌더링할 때 사용   

   ex) App컴포넌트를 #root 태그에 랜더링을 함

● Export, Import

[https://velog.io/@grinding\_hannah/React.JS-React-%EA%B0%95%EC%A2%8C-%ED%95%99%EC%8A%B5%EB%82%B4%EC%9A%A9-%EC%A0%95%EB%A6%AC](https://velog.io/@grinding_hannah/React.JS-React-%EA%B0%95%EC%A2%8C-%ED%95%99%EC%8A%B5%EB%82%B4%EC%9A%A9-%EC%A0%95%EB%A6%AC)