---
layout: post
title: React & ES6 Object Destructuring Assignment(구조분해할당)
categories: React.js
tags: [React.js]
---

State나 Props를 사용할 때 값에 접근을 용이하게 하기위해 구조분해할당을 사용

일반적으로 render() 안에서 분해함

```javascript
const { info } = this.state		// this.state.info
```

→ { info }를 사용하여 this.state.info 값에 접근할 수 있게함

```javascript
class App extends React.Component {
  const { name, nickname } = this.props
  const { state1, state2 } = this.state
  render() {
    return (
      <div>Hello {name}!! (nickname : {nickname})</div>
    )
  }
}
```