# [자바스크립트] 이벤트 위임(Event delegation)

- Title : [자바스크립트] 이벤트 위임(event delegation)
- Date : 2020-03-20
- Category : Javascript

# Why?

웹 개발자라면 이벤트 핸들러를 등록할 때 **캡처링**과 **버블링**을 적절하게 선택할 수 있어야 하지 않을까?
```javascript
    // 이벤트 캡처
    element1.addEventListener('click',doSomething2,true)
    // 이벤트 버블링
    element2.addEventListener('click',doSomething,false)
```
> 🔊 익명함수로 이벤트 핸들러를 정의할 수 있는 데 이 때 화살표 함수의 this 컨텍스트는 event.currentTarget이 아님을 알고 있어야 한다. function (e) {} 형태로 정의해야 this 컨텍스트가 event.currentTarget이 된다.

# addEventListener 동작 방식

addEventListener() 메서드의 마지막 argument의 값으로 이벤트 전파 방식을 지정할 수 있다. true면 캡쳐, false(기본값)면 버블링으로 이벤트가 전파된다. 아래 예시로 이해를 어떤식으로 전파되는 지 이해해보자.
```html
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width">
      <title>JS Bin</title>
    </head>
    <body>
      <div id="element1">
        element1
        <div id="element2">
           element2
          <div id="element3">element3</div
        </div>
      </div>
    </body>
    </html>
```
```javascript
    var element1 = document.getElementById("element1");
    var element2 = document.getElementById("element2");
    var element3 = document.getElementById("element3");
    function doSomething1(){
      console.log("doSomething1");
    }
    function doSomething2(){
      console.log("doSomething2");
    }
    function doSomething3(){
      console.log("doSomething3");
    }
```
🎈 3개의 element가 버블링을 사용한다면?
```javascript
    element1.addEventListener('click',doSomething1,false)
    element2.addEventListener('click',doSomething2,false)
    element3.addEventListener('click',doSomething3,false)
```
- element1 클릭시 출력 :  `doSomething1`
- element2 클릭시 출력 :  `doSomething2`→ `doSomething1`
- element3 클릭시 출력 :  `doSomething3`→ `doSomething2` -> `doSomething1`

🎈 3개의 element가 캡처링을 사용한다면?
```javascript
    element1.addEventListener('click',doSomething1,true)
    element2.addEventListener('click',doSomething2,true)
    element3.addEventListener('click',doSomething3,true)
```
- element1 클릭시 출력 :  `doSomething1`
- element2 클릭시 출력 :  `doSomething1`→ `doSomething2`
- element3 클릭시 출력 :  `doSomething1`→ `doSomething2` -> `doSomething3`

🎈 맨 밖의 엘리먼트는 버블링 내부 엘리먼트는 캡처링을 사용한다면?
```javascript
    element1.addEventListener('click',doSomething1,false)
    element2.addEventListener('click',doSomething2,true)
    element3.addEventListener('click',doSomething3,true)
```
- element1 클릭시 출력 :  `doSomething1`
- element2 클릭시 출력 :  `doSomething2`→ `doSomething1`
- element3 클릭시 출력 :  `doSomething2`→ `doSomething3` -> `doSomething1`

🎈 섞여있으면..?
```javascript
    document.body.addEventListener('click',doSomething0,true)
    element1.addEventListener('click',doSomething1,false)
    element2.addEventListener('click',doSomething2,true)
    element3.addEventListener('click',doSomething3,false)
```
element3 클릭시 출력 :  `doSomething0`→ `doSomething2` -> `doSomething3` → `doSomething1`

### 테스트 결과

- 이벤트 타켓에 이벤트가 발생하면 이벤트가 다른 이벤트 타켓(이벤트 핸들러가 등록된)으로 전파된다.
- 이벤트 캡처는 이벤트 타켓의 최상위 요소(단, 이벤트 핸들러가 등록된)부터 이벤트 타켓까지 이벤트 핸들러를 처리한다.
- 이벤트 버블링은 캡처와 반대로 이벤트 타켓부터 최상위 요소까지 이벤트 핸들러를 처리한다.
- 섞여있으면 캡처링 우선인 이벤트 타켓부터 처리한 후 버블링을 수행한

# 이벤트 버블링과 캡쳐

아래 사진은 DOM 트리에서 일어나는 이벤트 전파 방법이다.

[UI Events](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)

![Event%20delegation/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/Untitled.png)

캡처와 버블링은 두 요소(엘리먼트)가 해당 이벤트에 대한 핸들(함수)를 등록한 경우, 다른 요소 내에 중첩된 요소에서 발생하는 이벤트를 전파하는 방법이다. 이벤트 전파 모드로 요소가 이벤트를 수신하는 순서를 판별한다.

## 이벤트 전파..? 신경쓰기 싫어!

현재 이벤트 이후의 이벤트 전파(캡처링, 버블링)를 막고 싶다면 **`event.stopPropagation()`** 메서드를 사용하면 된다.

## 이벤트는 취소하고 전파는 하고 싶어!

**`event.preventDefault()`** 는 이벤트를 취소하지만 이벤트 전파를 막지 않는다. 전파를 막으려면 `event.stopPropagation()`을 사용해야한다.  주로 `a` 태그나 `submit` 태그 등 고유의 동작을 막고 원하는 이벤트 핸들러를 실행할 때 사용한다.

## 메모리 이슈

루프의 각 반복마다 새로운 익명 행들러 함수가 생성되는 방식으로 이벤트를 등록하지 말자. 따로 이벤트 핸들러를 생성한 후 등록하는 방식을 사용해야 메모리 소비가 줄고 `removeEventListener()`를 호출할 수 있다. 익명 함수는 참조가 유지되지않기에 이벤트 리스너를 제거할 수 없다.

# 이벤트 위임(Event delegation)

이벤트 발생 시 document 레벨까지 버블링 되어 올라가는 것을 활용하는 것으로 하위 요소에 각각 이벤트를 붙이지 않고 상위 요소에서 하위 요소의 이벤트들을 제어하는 방식이다.

- 동적인 엘리먼트에 대한 이벤트 처리 수월
- 하위 엘리먼트는 자유롭게 추가 삭제 가능
- 동일한 이벤트를 한 곳에서 관리할 수 있고 이벤트 핸들러 관리 쉽다.
- 생성되는 이벤트 핸들러 함수가 줄어 메모리 사용량이 줄고 메모리 누수 가능성이 감소된다.

참조

[왜 이벤트 위임(delegation)을 해야 하는가?](https://ui.toast.com/weekly-pick/ko_20160826/)

[EventTarget.addEventListener()](https://developer.mozilla.org/ko/docs/Web/API/EventTarget/addEventListener)