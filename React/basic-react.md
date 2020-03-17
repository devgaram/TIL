---
title: React 기반을 다져보자!
date: 2020-01-07
tags: [jsx]
categories: [react]
---

<span class="clr-grey">새로 알게 된 내용이 있으면 계속해서 추가할 예정입니다. </span>

> **참조 링크** <br/>
> - [리액트 공식 문서](https://reactjs-kr.firebaseapp.com/docs/hello-world.html)


# JSX
```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root') // ID가 root인 노드에 렌더링하겠다!
);
```

- JSX는 화면에서 볼 수 있는 내용에 대한 설명인 <code>React elements</code> 객체를 만든다.
- 표현식이므로 if문, for 반복, 변수 할당, 매개 변수로 사용 가능하다.
- Babel은 JSX를 <code>React.createElement()</code> 호출로 컴파일합니다.
- 리액트 요소는 <code>Immutable Objects</code>라서 한번 만들면 그 자식이나 속성을 변경할 수 없다. UI를 업데이트 하려면 새로운 요소를 만들어서 <code>ReactDOM.render()</code>에 전달해야한다.

> **Note:** <br/>
> 실제로 대부분의 React 어플리케이션은 ReactDOM.render() 를 한번만 호출한다. -> state를 이용한다.

# 순수 함수란?
순수 함수는 입력을 변경하지않으며 항상 동일한 입력에 대해 동일한 결과를 반환하는 함수다.

**순수 함수**
```javascript
function sum(a, b) {
  return a + b;
}
```
**순수 함수가 아님**
```javascript
function withdraw(account, amount) {
  account.total -= amount;
}
```

# Props
- 부모 컴포넌트에서 자식 컴포넌트로 전달된 데이터다.
- Props는 읽기전용이므로 수정할 수 없다.
- 모든 React 컴포넌트는 props와 관련한 동작을 할 때 <code>순수 함수</code>처럼 동작해야한다.

# State

## 1. 클래스 컴포넌트에서 state 사용하기
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  // mounting : Clock 이 DOM에 최초로 렌더링 될 때
  componentDidMount() {

  }

  // unmounting : DOM에서 Clock 을 삭제했을 때
  componentWillUnmount() {

  }
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

## 2. 함수형 컴포넌트에서 state 사용하기
```javascript
import React, { useEffect, useState } from 'react';
// ...

const PetSitterApplyContainer = () => {
  const [current, setCurrent] = useState(0) // 인자는 초기값

  // ...

  // 라이프사이클 훅
  useEffect(() => {
    function handleResize() {
      dispatch(resize(window.innerWidth, window.innerHeight))
    }
    window.addEventListener('resize', handleResize)
  });

  const next = () => {
    setCurrent(current + 1)
  }

  const prev = () => {
    setCurrent(current - 1)
  }
  
  return (
    <PetSitterApply 
      current={current} />
  )
}
```
- state는 React 컴포넌트가 유저 액션, 네트워크 응답, 기타 등등에 대한 응답으로 시간 경과에 따라 출력을 변경할 수 있게 한다.
- State는 로컬이며 캡슐화되어있다 : 부모 컴포넌트나 자식 컴포넌트는 특정 컴포넌트의 state 유무를 알 수 없으며 해당 컴포넌트가 함수나 클래스로 선언되었는 지 알 수 없기 때문
- <code>componentDidMount() 훅</code> : 컴포넌트 출력이 DOM에 렌더링 된 이후 동작한다.
- <code>componentWillUnmount() 훅</code> : 컴포넌트가 DOM에서 삭제된 이후 동작한다.
- <code>useEffect</code> : 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정 할 수 있는 Hook으로 클래스형 컴포넌트의 componentDidMount 와 componentDidUpdate 를 합친 형태다.

## 3. State 바르게 사용하기
- State를 직접 수정하지말기

```javascript
// Wrong
this.state.comment = 'Hello';

// Correct
this.setState({comment: 'Hello'});
```

- this.props 및 this.state가 비동기로 업데이트될 수 있다는 것을 고려하기

```javascript
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});

// Correct : 이전 state를 인수로 받음
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

# 하향식(top-down) 혹은 단방향(unidirectional) 데이터 흐름
- 모든 state는 항상 특정 컴포넌트가 가지며, 해당 state에서 파생된 모든 데이터 또는 UI는 트리의 컴포넌트 <code>아래(below)</code>에만 영향을 미친다.
- 컴포넌트는 자신의 state를 자식 컴포넌트에 props 로 내려줄 수 있다. => <code>컴포넌트 트리 == props의 폭포</code>

# Refs
일반적인 리액트 데이터 플로우에서 부모 컴포넌트와 자식 컴포넌트는 <code>props</code>를 통해서만 통신할 수 있어서 자식 컴포넌트를 수정하려면 새로운 <code>props</code>와 함께 다시 렌더링해야한다. 그럼 일반적인 데이터 플로우 밖에서 자식 컴포넌트(컴포넌트 인스턴스 or DOM)에 직접 접근하려면 어떻게 해야할까? <code>Refs</code>를 사용하면 되지만 공식문서에서는 Refs 보다는 state를 이용하기로 권장한다.


```javascript
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // Explicitly focus the text input using the raw DOM API
    this.textInput.focus();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    // element in an instance field (for example, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```
- React는 컴포넌트가 마운트될 때 DOM 요소와 함께 ref 콜백을 호출하며 언마운트될 때 null 과 함께 호출한다. ref 콜백은 componentDidMount 나 componentDidUpdate 라이프사이클 훅 전에 호출된다.
- ref 속성을 HTML 요소에서 사용하면, ref 콜백은 기본 DOM 요소를 인수로 받는다.

> Refs는 언제 사용하면 좋을까?
> - input/textarea 포커스 제어, 텍스트 선택, 미디어 재생을 관리할 때
> - 명령형 애니메이션을 발동시킬 때
> - 써드 파티 DOM 라이브러리를 통합할 때

