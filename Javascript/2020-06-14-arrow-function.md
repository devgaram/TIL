# Arrow function

Date: Jun 14, 2020

- Title : Arrow function
- Date : 2020-06-14
- Category : Javascript

# 화살표 함수

ES6에 익명 함수를 간단하게 표현할 수 있는 화살표 표현식이 추가되었다.

## 기존 익명 함수 vs 화살표 함수

콜백 함수로 실행될 때 기존의 익명 함수는 글로벌 컨텍스트에 접근 하였으나, 함수 화살표 표현식은 콜백 함수를 할당한 당시의 컨텍스트를 그대로 활용한다. 

```javascript
var name = 'global';
function Person() {
	this.name = 'garam';

	setTimeout(() => alert(`my name is ${this.name}`), 100); //garam
	setTimeout(function() {
		alert(`Global name is ${this.name}`); // global
	}, 100);
}

let person = new Person();
```

화살표 함수는 함수 선언 시 this가 바인딩 할 객체가 **정적**으로 결정된다. 즉 일반 함수와 달리 화살표 함수는 항상 **상위 스코프의 this**를 가리킨다. 이를 `Lexical this`라고 한다.

> 자바 스크립트는 함수 호출 시 this가 바인딩 할 객체가 **동적**으로 결정된다. 함수 선언 시 this에 바인딩 할 객체가 정적으로 결정되는 것이 아니라 **함수를 호출할 때 함수가 어떻게 호출 되었는지에 따라 this가 바인딩할 객체를 동적**으로 결정한다.

```javascript
const test = {
  name: 'test object',
  createAnonFunction: function() {
    return function() {
      console.log(this.name);
      console.log(arguments);
    };
  },

  createArrowFunction: function() {
    return () => {
      console.log(this.name);
      console.log(arguments);
    };
  }
};

const anon = test.createAnonFunction('hello', 'world'); 
// undefined
// {}
const arrow = test.createArrowFunction('hello', 'world');
// test object
// { '0': 'hello', '1': 'world' }
```

기존 익명 함수는 함수가 호출 되었을 때의 컨텍스트를 가진다. 즉 `test.createAnonFunction`이 글로벌 컨텍스트에서 호출 되었기에 `this.name`과 `arguments` 객체를 얻을 수 없다. 

## 화살표 함수 언제 쓰지 말아야 할까?

### 객체의 메소드를 정의할 때

```javascript
const calculate = {
  array: [1, 2, 3],
  sum: () => {
    console.log(this === window); // => true
    return this.array.reduce((result, item) => result + item);
  }
};
console.log(this === window); // => true
// Throws "TypeError: Cannot read property 'reduce' of undefined"
calculate.sum();
```

메소드로 정의한 화살표 함수 내부의 this는 메소드를 소유한 객체를 가리키지 않고 상위 컨텍스트인 `window`를 가리킨다. 아래와 같이 사용 해야 한다.

```javascript
const calculate = {  
  array: [1, 2, 3],
  sum() {
    console.log(this === calculate); // => true
    return this.array.reduce((result, item) => result + item);
  }
};
calculate.sum(); // => 6
```

### 프로토타입 방식으로 객체 메소드 정의할 때

```javascript
function MyCat(name) {
  this.catName = name;
}
MyCat.prototype.sayCatName = () => {
  console.log(this === window); // => true
  return this.catName;
};
const cat = new MyCat('Mew');
cat.sayCatName(); // => undefined
```

```javascript
function MyCat(name) {
  this.catName = name;
}
MyCat.prototype.sayCatName = function() {
  console.log(this === cat); // => true
  return this.catName;
};
const cat = new MyCat('Mew');
cat.sayCatName(); // => 'Mew'
```

### 이벤트 리스너 콜백함수

```javascript
const button = document.getElementById('myButton');
button.addEventListener('click', () => {
  console.log(this === window); // => true
  this.innerHTML = 'Clicked button';
});
```

`this`는 글로벌 컨텍스트에 바인딩 된다. 클릭 이벤트가 발생하면 브라우저는 버튼 컨텍스트와 함께 핸들러 함수를 실행 시킨다. 화살표 표현식으로 정의된 함수의 경우는 정적으로 `this`가 바인딩 되기 때문에 `this`가 동적으로 변경되는 이벤트 콜백 함수로 적절하지 않다.

```javascript
const button = document.getElementById('myButton');
button.addEventListener('click', function() {
  console.log(this === button); // => true
  this.innerHTML = 'Clicked button';
});
```

기존 익명 함수는 타켓 엘리먼트에 의존하여 this를 바꾸는 것을 허용한다.

### 생성자 함수

모든 객체는 자신의 `prototype`으로 부터 `constructor` 속성을 상속하기 때문에 `prototype` 속성을 가지고 있어야 한다. 하지만 화살표 표현식으로 정의된 함수는 `prototype` 속성이 없어서 생성자 함수로 사용될 수 없다.

```javascript
const Foo = () => {};

// 화살표 함수는 prototype 프로퍼티가 없다
console.log(Foo.hasOwnProperty('prototype')); // false

const foo = new Foo(); // TypeError: Foo is not a constructor
```
