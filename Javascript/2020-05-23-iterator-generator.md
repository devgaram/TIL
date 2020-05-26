# 자바스크립트의 iterable, iterator 프로토콜과 generator

- Title : 자바스크립트의 iterable, iterator 프로토콜과 generator
- Date : 2020-05-23
- Category : Javascript

# iterable 프로토콜

- iterable 프로토콜은 반복 가능한 객체를 정의하는 방법(규약)으로 `for..of` 를 통해 value를 반복할 수 있다.
- 반복 가능한 내장 객체로 `Array`, `Map`, `Set`, `String` 등이 있으며 객체는`[Symbol.iterator]` 속성 키를 가진다. *이 객체 들의 프로토타입 객체들이 `@@iterator` 메소드를 가지기 때문에 반복이 가능하다.*
- `Promise.all(iterable)`, `Array.from()` 도 iterable을 허용한다.
- 반복 가능한 객체를 만들고 싶다면 객체의 `[Symbol.iterator]` 속성에 `@@iterator` 메소드를 구현 하면 된다. 이 메소드는 iterator 프로토콜을 따르며 인수 없이 호출 되고 iterator 객체를 반환한다.

반복 가능한 객체(iterable)는 `for-of` , `spread operator`, `yield*`, `구조 분해 할당`에 함께 사용된다.

```java
for(let value of ['a', 'b', 'c']){
    console.log(value)
}
// "a"
// "b"
// "c"

[...'abc'] // ["a", "b", "c"]

function* gen(){
  yield* ['a', 'b', 'c']
}

gen().next() // { value:"a", done:false }

[a, b, c] = new Set(['a', 'b', 'c'])
a // "a"
```

String은 반복 가능한 내장 객체로 `[Symbol.iterator]` 속성에 `@@iterator` 메소드가 있다.

```java
const someString = "hi";
typeof someString[Symbol.iterator]; // function
const iterator = someString[Symbol.iterator](); // StringIterator {}
iterator + "" // [Object String Iterator]

iterator.next(); // { value: "h", done: false }
iterator.next(); // { value: "i", done: false }
iterator.next(); // { value: undefined, done: true }

[...someString] // [ "h", "i" ]
```

반면에 일반 객체는 `@@iterator` 없어서 `undefined` 값을 반환한다. *물론 정의가 가능하다.*

```java
const testObject = new Object();
typeof testObject[Symbol.iterator] // undefined
```

# iterator 프로토콜

- 반복 가능한 객체의 value들을 시퀀스(어떠한 일련의 순서)대로 처리하는 방법(규약)이다.

### iterator 객체 구현 규칙

- `next()` 메소드가 있어야 한다. 이 메소드는 `done`, `value` 속성을 가진 객체를 반환한다.
    - `done`(boolean): iterator가 마지막 반복 작업을 끝내면 true, 작업이 남아 있으면 false다. *done이 true이면 반환되는 객체의 value 프로퍼티의 값은 undefined다.*
    - `value`: iterator에서 반환되는 값으로 done이 true면 생략될 수 있다.

아래 코드는 문자를 하나씩 반환하는 String의 `@@iterator` 메소드를  "bye" 만 반환하고 끝내도록 재 정의한 예제다.

```java
const someString = new String("hi");

someString[Symbol.iteraror] = function() {
	return { // iterator 객체를 리턴
		next: function() {
			if (this._first) {
				this._first = false;
				return { value: "bye", done: false };
			} else {
				return { done: true };
			}
		},
		_first: true
	};
};
```

# Generator

## Generator 함수

- 실행이 연속적이지 않는 작업을 iterator 처럼 사용할 수 있게 해준다.
- `function*` 키워드를 통해 선언되는 Generator 함수는 호출 되어도 즉시 실행되지 않고, 대신 함수를 위한 Iterator 객체(Generator 객체)가 반환됩니다
- `next()` 메소드를 호출하면 Generator 함수가 실행되어 `yield` 문을 만날 때까지 진행하고, 해당 yield가 반환하는 객체를 리턴 한다.  반환하는 객체는 yield문이 반환할 값(yielded value)을 나타내는 `value` 속성과, Generator 함수 안의 모든 yield 문의 실행 여부를 표시하는 boolean 타입의 `done` 속성을 갖는다.

> `yield*`: 다른 generator 또는 iterable 객체에 yield를 위임할 때 쓰이며 `yield [iterable 객체를 반환하는 표현식]` 방식으로 사용한다.

## Generator 객체

- generator function에서 반환된 값으로 iterator, iterable 프로토콜을 준수한다.
- `Generator.prototype.next()`: `yield` 표현을 통해 yield된 값을 반환
- `Generator.prototype.return()`: 주어진 값을 반환하고 Generator 종료
- `Generator.prototype.throw()`:  Generator의 실행을 재개시키고 Generator 함수의 실행 문맥 속으로 error를 주입하며 done, value 프로퍼티를 가진 객체를 반환한다.

```java
function* gen() {
	while(true) {
		try {
		  yield 1;
		  yield 2;
		  yield 3;
		} catch(e) {
			console.log('Error caught!');
		}
	}
}

var g = gen(); // "Generator { }"

g.next();        // { value: 1, done: false }
g.throw(new Error("Something went wrong"));
// "Error caught!"
// { value: 1, done: false }
g.return("foo"); // { value: "foo", done: true }
g.next();        // { value: undefined, done: true }
```

반복 가능한 객체를 만들기 위해 `[Symbol.iterator]` 속성에 Generator 객체를 아래와 같이 정의할 수 있다.

```java
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable]; // [1, 2, 3]
```

무한 iterable 객체 만드는 방법

```java
function* idMaker(){
    var index = 0;
    while(true)
        yield index++;
}

var gen = idMaker(); // "Generator { }"

console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
```
