---
layout: post
title: React Life Cycle
categories: React.js
tags: [React.js]
---

![](https://blog.kakaocdn.net/dn/cqaew1/btqTcQcIAdV/8BGJx1ybI0Wu5Gpte5xVFk/img.png)

1\. 생성시

constructor\-> render -> componentDidMount

2\. 업데이트시

New props, setState(), forceUpdate() 실행\[업데이트\] -> render -> componentDidUpdate

3\. 소멸시

componentWillUnmount

```javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    console.log("1. ----constructor 실행!!----");
    this.state = {
      name: "LifeCicle"
    };
  }

  componentDidMount() {
    console.log("3. ----componentDidMount 실행!!----");
    console.log("setState() 호출");
    this.setState({
      name: "updated"
    });
  }

  componentDidUpdate(prevProps, prevState) {
    console.log("4. ----componentDidUpdate 실행!!----");
    // 업데이트 되기 전 state
    console.log(prevState, "업데이트 전");
    // 업데이트 된 후의 현재 state
    console.log(this.state, "업데이트 후");
  }

  componentWillUnmount() {
    console.log("5. ----componentWillUnMount 실행!!----");		<!-- 직접확인불가 -->
  }

  render() {
    console.log("2. ----render 실행!!----");
    return (
      <div className="App">
        <h1>Hello CodeSandbox</h1>
        <h2>Start editing to see some magic happen!</h2>
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById("root"));


```

```console
1. ----constructor 실행!!----
2. ----render 실행!!----
3. ----componentDidMount 실행!!----
setState() 호출
2. ----render 실행!!----
4. ----componentDidUpdate 실행!!----
{name: "LifeCicle"} "업데이트 전"
{name: "updated"} "업데이트 전"
```

[projects.wojtekmaj.pl/react-lifecycle-methods-diagram/](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
