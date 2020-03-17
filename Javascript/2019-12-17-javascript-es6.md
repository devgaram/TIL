---
layout: post-content
title: ES6를 모르면 React 배우기가 어려워요.
date: 2019-12-17
tags: [es6, react]
categories: [javascript]
---

React를 배워볼까? 하고 두근거리는 마음으로 React 공식 문서에 방문해서 자습서부터 차근차근 읽어본 적이 있습니다.    
그런데, 으잉? 이건 대체 뭐지? 제게 커다란 장벽이 나타났습니다. 그 것은 바로 바로... **ES6 문법!!!**    
그래서 준비했습니다. <span class="clr-note">"ES6를 모르면 React 배우기가 어려워요!"</span> 지금부터 저와 함께 ES6 최신 문법을 정리해봅시다!

# 변수 선언하기

## 1. const
블록(중괄호 {}) 유효 범위의 상수를 선언하며 재할당 및 재선언이 불가능합니다.

```javascript
const number = 42;

try {
  number = 99;
} catch(err) {
  console.log(err); // TypeError: Assignment to constant variable.
}

console.log(number); // 42
```
## 2. let
<code class="codetainer">let</code> 키워드를 사용하면 변수의 유효 범위를 블록 {} 안으로 한정시킬 수 있고 글로벌 변수의 값을 보호할 수 있습니다.

```javascript
var topic = "js"
if (topic) {
  var topic = "리액트"
  console.log('블록', topic) // 블록 리액트
}
console.log('글로벌', topic) // 글로벌 리액트
```
위 예제의 if 블록 안의 topic 변수와 if 블록 밖의 topic 변수는 같은 변수입니다.    
<code class="codetainer">var</code> 키워드는 함수 유효 범위를 가지기 때문입니다. 자바스크립트의 호이스팅에 의해 함수 안에 정의된 변수는 함수의 맨 앞에서 정의되고 <code class="codetainer">undefined</code>로 초기화되기 때문에 두 topic 변수가 사실상 같은 변수가 됩니다.
<br/><br/>

```javascript
var topic = "js"
if (topic) {
  let topic = "리액트"
  console.log('블록', topic) // 블록 리액트
}
console.log('글로벌', topic) // 글로벌 js
```
if 블록 안의 변수가 if 블록 밖의 변수에 영향을 끼치지 않게 하려면 <code class="codetainer">let</code> 키워드를 사용하면 됩니다. <code class="codetainer">let</code>은 블록 유효 범위이므로 호이스팅 시 블록의 맨 앞에서 정의되고 초기화 됩니다.
<br/><br/>

```javascript
console.log(name) // Uncaught ReferenceError: Cannot access 'name' before initialization
let name = 'lee' 

console.log(age); // undefined
var age = '20'
```
단, var로 선언된 변수는 선언과 초기화가 동시에 진행되고 let을 선언된 변수는 호이스팅되어 선언 단계가 이뤄지나 초기화 단계는 실제 let이 사용된 코드에 도착할 때 이루어집니다. 그래서 let 키워드로 선언한 변수를 초기화 단계 이전에 접근하면 reference 에러가 발생합니다.

## 3. 템플릿 문자열
템플릿 문자열 덕분에 더하기 기호 + 없이 편하게 문자열과 변수를 이어 붙인 string을 생성할 수 있습니다.    
<code class="codetainer">${}</code> 안에는 자바스크립트 변수, 식 어떤 것이든 가능하며 템플릿 문자열은 공백, 탭, 개행 문자를 사용할 수 있습니다.

```javascript
console.log(`${lastName}, ${firstName} ${middleName}`)
```
<span class="clr-grey"></span>
<span class="clr-note"></span>

## 4. 디폴트 파라미터
이제 함수를 호출 시 인자 값이 넘어오지 않았다면 디폴트 값을 사용할 수 있습니다.

```javascript
function func(name="lee", age="20") {
  console.log(`${name} : ${age}`)
}
func() // lee : 20
```
함수 호출 시 인자 넘기지 않아도 디폴트 값을 사용해 함수가 정상적으로 실행됩니다. 문자열뿐 아니라 객체 등 다양한 타입의 값을 디폴트로 사용할 수 있습니다.

# 화살표 함수 =>
화살표 함수를 사용하면 function 키워드 없이도 함수를 만들 수 있고 return을 사용하지 않아도 식을 계산한 값이 자동으로 반환됩니다.

```javascript
var materials = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];
materials.map(({length}) => length); // [8, 6, 7, 9]
```
<br/>
또한, <span class="clr-note">화살표 함수는 this를 새로 바인딩 하지 않습니다.</span> 대신 코드에서 바로 바깥의 함수(혹은 class)의 this 값을 사용합니다. 뿐만 아니라 <code class="codetainer">arguments, super, new.target</code>을 바인딩 하지 않습니다.

```javascript
var obj = {
  i: 10,
  b: () => console.log(this.i, this),
  c: function() {
    console.log( this.i, this)
  }
}
obj.b(); // prints undefined, Window
obj.c(); // prints 10, Object {...}
```
<br/>
this를 바인딩 하지 않기 때문에 메소드 함수에는 사용하지 않아야 하며 생성자로서도 사용할 수 없기 때문에 <code class="codetainer">new</code>와 함께 사용하면 오류가 발생합니다.

```javascript
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```

# ES6 객체와 배열

## 1. 구조 분해(Destructuring)
구조 분해를 사용하면 객체의 필드를 원하는 변수에 대입할 수 있습니다.

다음은 sandwich를 분해해서 bread와 meat 필드를 같은 이름의 변수에 넣어주는 코드입니다. 두 변수의 값은 sandwich에 있는 같은 이름의 필드 값으로 초기화되지만, 두 변수를 변경해도 원래의 필드 값은 바뀌지는 않습니다.
```javascript
var sandwich = {
  bread: "크런치",
  meat: "참치",
  cheese: "스위스",
  toppings: ["상추", "토마토", "머스타드"]
}

var {bread, meat} = sandwich

console.log(bread, meat) // 크런치 참치
```


객체를 분해해서 함수의 인자로 넘길 수 있습니다.
```javascript
var func = ({firstName}) => {
  console.log(`이름은 ${firstName}`)
}

func({
  firstName: 'garam',
  lastName: 'lee'
}) // 이름은 lee
```


배열의 경우도 비슷합니다.
```javascript
var x = [1, 2, 3, 4, 5];
var [y, z] = x;
console.log(y,z); // 1 2
```


변수 선언이 분리되어도 값 할딩이 가능합니다.
```javascript
var a, b;
[a, b] = [1, 2];
console.log(a, b); // 1 2
```


기본값을 할당하여 분해한 값이 undefined면 기본 값을 사용합니다.
```javascript
var a, b;
[a=5, b=7] = [1];
console.log(a); // 1
console.log(b); // 7
```


두 변수 값 교환이 가능합니다.
```javascript
var a = 1;
var b = 3;

[a, b] = [b, a];
console.log(a); // 3
console.log(b); // 1
```


불필요한 값을 콤마를 사용해 생략하는 리스트 매칭을 사용할 수 있습니다. 무시하고 싶은 원소 위치에 콤마를 넣으면 리스트 매칭이 됩니다.
```javascript
var [,,third] = ["a","b","c"]
console.log(third) // c
```

## 2. 객체 리터럴 개선
구조 분해의 반대라고 할 수 있으며 객체 리터럴 개선을 사용하면 현재 영역에 있는 변수를 객체의 필드로 묶을 수 있습니다.

```javascript
var name = "lee"
var age = "20"
var print = function() {
  console.log(`${this.name} : ${this.age}`)
}
var person = {name, age, print}
person.print() // lee : 20
```

객체 리터럴 개선으로 변수를 객체의 필드로 대입할 수 있으며 function 키워드를 입력하지 않고 메서드를 정의할 수 있다.
```javascript
// 예전 방식
var obj = {
  name: name,
  age: age,
  print: function() {
    console.log(`${name} : ${age}`)
  }
}

// 개선된 방식
const obj = {
  name,
  age,
  print() {
    console.log(`${name} : ${age}`)
  }
}
```

## 3. 스프레드 연산자
스프레드 연산자는 세 개의 점(...)으로 이루어진 연산자로, 
<code class="codetainer">함수호출, 배열 리터럴과 문자열, 객체 리터럴</code>에서 사용할 수 있습니다.

이전에는 두 배열의 모든 원소가 들어간 새로운 배열을 만들기 위해서 <code class="codetainer">push(), splice(), concat() </code> 등을 사용해야 했습니다. 이제 스프레드 연산자를 통해 쉽게 만들 수 있습니다.
```javascript
// 이전 방식
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1 = arr1.concat(arr2);

// 전개 방식
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1 = [...arr1, ...arr2]; // arr1은 [0,1,2,3,4,5]

// 새로운 배열 생성
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
var arr3 = [...arr1, ...arr2] // [0,1,2,3,4,5]

// 중간 조합도 가능
var arr1 = [0, 1, 2];
var arr2 = [3, ...arr1, 4, 5]; // 3,0,1,2,4,5

// 나머지 원소 얻기 가능
var arr1 = [0, 1, 2, 3];
var [first, ...rest] = arr1;
console.log(rest); // 1,2,3
```

스프레드 연산자는 원본 배열을 변경하지 않고 복사본을 만듭니다.     
다음 예제를 통해 실제로 확인해보자. arr1 배열의 마지막 원소를 변수에 담으려고 <code class="codetainer">Array.reverse</code>를 이용해 배열을 뒤집고 첫번째 원소를 변수에 넣는 예제입니다.
```javascript
// 원본 배열이 변경된다.
var arr1 = [0, 1, 2];
var [last] = arr1.reverse();
console.log(last); // 2
console.log(arr1); // 2 1 0

// 전개 연산을 통해 원본 배열 유지하기
var arr1 = [0, 1, 2];
var [last] = [...arr1].reverse();
console.log(last); // 2
console.log(arr1); // 0,1,2
```

객체에 사용할 수도 있습니다. 사용법은 배열과 유사합니다.
```javascript
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };
var obj3 = "obj"
var mergedObj = { ...obj1, ...obj2, obj3};
console.log(mergedObj); // { foo: "baz", x: 42, y: 13, obj3: "obj" }
```

함수 호출할 때도 사용할 수 있어서 배열의 엘리먼트를 함수의 인자로 사용하고자 할 때<code class="codetainer">Function.prototype.apply()</code>를 대체할 수 있습니다.
```javascript
// 이전 방식
function myFunction(x, y, z) { }
var args = [0, 1, 2];
myFunction.apply(null, args);

// 전개 방식
function myFunction(x, y, z) { }
var args = [0, 1, 2];
myFunction(...args);

// new 키워드와 함께 사용할 수 있습니다.
var dateFields = [1970, 0, 1];  // 1 Jan 1970
var d = new Date(...dateFields);
```
# 클래스
이전에는 공식적으로 클래스가 없어서 타입을 함수로 정의하고 그 함수 객체에 있는 프로토타입을 사용해 메서드를 정의했습니다. ES6부터 클래스 선언이 추가되어서 쉽게 클래스 패턴 생성이 가능해졌습니다.

```javascript
var Foo = class {
  constructor() {}
  bar() {
    return "Hello World!";
  }
};

var instance = new Foo();
instance.bar(); // "Hello World!"
Foo.name; // ""
```
class 키워드를 사용하더라도 내부적으로는 자바스크립트의 프로토타입 상속을 사용하고 있습니다. <code class="codetainer">Foo.prototype</code>을 콘솔에 찍어보면 프로토타입에 생성자와 bar 메서드가 있는 것을 확인할 수 있을 것입니다.

# ES6 모듈
모듈은 다른 자바스크립트 파일의 코드를 쉽게 불러서 활용할 수 있는 재사용 가능한 코드 조각입니다. <code class="codetainer">export</code>와 <code class="codetainer">import</code> 키워드를 이용하면 됩니다.

<code class="codetainer">export</code>를 사용해 다른 모듈에서 활용할 수 있도록 이름(함수, 객체, 변수, 상수 등)을 외부에 익스포트할 수 있습니다.

```javascript
// export_1.js
// print, log 함수를 외부에 export
// 로컬 선언됨
export const print(message) => log(message, new Date())
export const log(message, timestamp) => console.log(`${timestamp.toString()} : ${message}`)

// export_2.js
// 단 하나의 이름만 외부에 export 하고 싶다면 export default 사용
const freel = new Date();
export default freel;
```

<code class="codetainer">import</code>를 사용해 다른 자바스크립트 파일을 불러와 사용할 수 있습니다. 임포트 시 객체 구조 분해를 사용할 수 있으며, <code class="codetainer">export default</code>를 사용한 경우 구조분해 없이 한 이름으로 부를 수 있습니다.

```javascript
import { print, log } from ./export_1
import freel from ./export_2

// import * 를 사용하여 다른 모듈에서 가져온 모든 이름을 사용자가 정한 로컬 이름 공간 안에 가둘 수 있음
import * from fns from ./export_1
```

# 커먼JS
모든 버전의 노드에서 지원하는 일반적인 모듈 패턴입니다. 커먼 JS를 사용하여 객체를 <code class="codetainer">module.exports</code>를 사용해 익스포트할 수 있습니다.

```javascript
//export.js
const print(message) => log(message, new Date())
const log(message, timestamp) => console.log(`${timestamp.toString()} : ${message}`)

module.exports = {print, log}
```

```javascript
const { print, log } = require('./export')
```

# ES6에서 비동기 프로그래밍
나중에 따로 정리할 예정.

## 1. promises


## 2. 제너레이터