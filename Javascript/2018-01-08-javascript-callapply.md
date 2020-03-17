---
layout: post-content
title: (JS) call()과 apply()
date: 2018-01-08
tags:
 - javascript
category: [javascript]
---

call() 과 apply() 메서드를 이용하여 명시적으로 this에 바인딩할 수 있습니다.

---

모든 함수의 부모 객체인 Function.prototype 객체의 메서드라서 모든 함수에서 호출 가능합니다.

## Function.prototype.apply()
```javascript
fun.apply(thisArg, [argArray])
```
fun 메서드를 호출할 때, fun 내부의 this를 매개변수인 thisArg로 바인딩 시킵니다. 
argArray는 fun 메서드에서 인자로 사용됩니다.   
<span class="clr-grey">argArray : 배열리터럴, Array 객체</span>

## Function.prototype.call()
```javascript
fun.call(thisArg[,arg1[,arg2[,....]]])
```
apply와 기능이 같으며, 배열형태가 아닌 각각의 하나의 인자형태로 값을 넘긴다는 차이점이 있습니다.

# 추가 개념

## 객체의 메서드를 호출할 때, 객체 메서드 내부의 this는?
해당 메서드를 호출한 객체

## 함수를 호출할 때, 함수 내부의 this는? 
전역객체에 바인딩 된다. window
*내부함수를 호출했을 때도, 내부 this는 window!

## 생성자 함수를 호출할 때, 생성자 함수 내부의 this는?
생성자 함수 코드가 실행되기 전 생성되는 빈 객체    
이 객체는 부모인 프로토타입과 연결되어있으므로 부모의 프로퍼티와 메서드를 사용할 수 있다.

```javascript
function Person() {
    this.age = 0;    // (1)
    setInterval( function growUp() {
        this.age++;    // (2)
    },1000); 
} 
var p  = new Person();
```
(1)의 this는 생성자 함수를 호출하면서 만들어지는 빈 객체    
(2)의 this는 window 전역객체를 가르킨다.

그래서, 위의 코드는 1초마다 나이가 1씩 증가하는 결과를 얻을 수 없다.

그렇다면, (2)가 Person 생성자로 만들어진 객체로 바인딩되게 하고 싶으며 어떻게 해야할까?

아래 코드와 같이 비전역 변수에 할당하여 해결할 수 있다.

```javascript
function Person(){
    var that = this;
    that.age = 0;     
 
    setInterval( function growUp(){
        that.age++;
    }, 1000); 
}
```
ES6의 화살표 함수를 이용하면 비전역 변수를 이용하지 않고도 생성자 객체에 바인딩 시킬 수 있다.    
화살표 함수는 자신만의 this를 생성하지 않기 때문이다!!

```javascript
function Person() {
    this.age = 0;     
 
    setInterval( () => {
        this.age++;
    }, 1000); 
}
```
위 화살표 함수 내 this는 정확히 Person() 생성자를 통해 생성된 객체를 가리키게 된다!

[출처](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/%EC%95%A0%EB%A1%9C%EC%9A%B0_%ED%8E%91%EC%85%98)