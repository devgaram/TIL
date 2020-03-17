---
title: 합성으로 컴포넌트에 다른 컴포넌트를 담아보자.
date: 2020-01-13
tags: [Composition]
categories: [react]
---

Modals을 만들면서 로그인 모달, 메모 모달 등 기능에 따라 모달 바디에 다른 UI를 보여주고 싶었다. 엘리먼트도 컴포넌트에 전달할 수 있을 까? 라는 의문으로 검색해보니 **Composition**이 나왔다.

# 컴포넌트에 다른 컴포넌트를 담고 싶다면?

## 예시 1

<code>props.children</code>을 사용하여 자식 엘리먼트를 그대로 출력할 수 있다.

```javascript
// ...
import Login from "./login" // 로그인 UI 컴포넌트
import ModalCard from "./modalCard" // 모달 컴포넌트

const Bio = () => {
  // ...
  return (
    <>
      <ModalCard>
        <Login />
      </ModalCard>
    </>
  )
}
```
ModalCard JSX 태그 안에 있는 것들이 아래와 같이 ModalCard 컴포넌트의 children prop으로 전달됩니다.

```javascript
// ...
const ModalCard = (props) => {
  return (
    <>
      <div className="modal is-active">
        <div className="modal-background"></div>
        <div className="modal-card">
          <header className="modal-card-head">
            <p className="modal-card-title"></p>
            <button className="delete" aria-label="close"></button>
          </header>
          <section className="modal-card-body">
            {props.children}
          </section>
        </div>
      </div>
    </>
  )
}
// ...
```

## 예시 2

React에서 prop으로 전달할 수 있는 것에는 제한이 없기 때문에 이렇게도 된다.

```javascript
// ...
import ModalCard from "./modalCard"
import Login from "./login"

const Bio = () => {
  // ...
  return (
    <>
      {// ...}
      <ModalCard headTitle={`Who are you?`} ContentComponent={Login} isActive={isActive}/>    
    </>
  )
}
```

```javascript
// ...
const ModalCard = ({ 
  headTitle,
  ContentComponent,
  isActive,
 }) => {
  
  return (
    <>
      <div className="modal">
        <div className="modal-background"></div>
        <div className="modal-card">
          <header className="modal-card-head">
            <p className="modal-card-title">{headTitle}</p>
            <button className="delete" aria-label="close"></button>
          </header>
          <section className="modal-card-body">
            <ContentComponent />
          </section>
        </div>
      </div>
    </>
  )
}
```

끝!