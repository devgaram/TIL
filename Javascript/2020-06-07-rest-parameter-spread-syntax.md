# Rest 파라미터와 Spread 연산자

- Title : Rest 파라미터와 Spread 연산자
- Date : 2020-06-07
- Category : Javascript

# Rest 파라미터 `...`

함수의 `arguments` 객체로 인해 유발되는 보일러 플레이트 코드를 줄일 목적으로 `Rest 파라미터` 가 도입되었다. `Rest 파라미터`는 함수의 마지막 파라미터 앞에 `...` 를 붙여 사용한다. 단 마지막에 한번만 사용해야 한다.

아래 예제를 보면 arguments 객체 대신 rest 파라미터를 사용해야 하는 이유를 알 수 있다.

```jsx
// Rest 파라미터 이전에, "arguments" 는 다음을 사용해 일반적인 배열로 변환될 수 있음
function f(a, b) {

  var normalArray = Array.prototype.slice.call(arguments);
  // -- 또는 --
  var normalArray = [].slice.call(arguments);
  // -- 또는 --
  var normalArray = Array.from(arguments);

  var first = normalArray.shift(); // OK, 첫 번째 인수를 반환
  var first = arguments.shift(); // ERROR (arguments 가 일반적인 배열이 아님)

}

// 이제 rest 파라미터를 사용해 쉽게 일반적인 배열에 접근할 수 있음

function f(...args) {
  var normalArray = args;
  var first = normalArray.shift(); // OK, 첫 번째 인수를 반환
}
```

## 1. `Rest` 파라미터와 `arguments` 객체의 차이를 자세히 알아보자.

- arguments 객체는 함수로 전달된 모든 인수를 포함한다. 즉 Rest 파라미터처럼 부분적으로 얻을 수 없다.
- arguments 객체는 배열 형태의 객체라 Array에 있는 sort, map, forEach, pop 과 같은 메서드를 사용할 수 없다. 반면에 Rest는 표준 배열이라 해당 메서드를 사용할 수 있다.

> 배열 형태의 객체는 length 속성과 더불어 0부터 인덱스 된 다른 속성을 가지고 있지만, Array의 forEach, map과 같은 내장 메서드를 가지고 있지 않다.

> 화살표 함수는 arguments 객체가 없다. 만약 화살표 함수에서 arguments 객체에 접근한다면 스코프 체인을 따라 일반 함수의 arguments 값을 가져오게 된다. 뿐만 아니라 화살표 함수는 자체 this가 없다.

## 2. 인자의 개수가 유동적일 때 Rest Parameter가 유용하다.

아래 예제는 인자로 전달되는 숫자들의 합을 구하는 함수다. 만약 Rest Parameter를 쓰지 않았다면?..

```jsx
function sum(...theArgs) {
  return theArgs.reduce((previous, current) => {
    return previous + current;
  });
}

console.log(sum(1, 2, 3));
// expected output: 6

console.log(sum(1, 2, 3, 4));
// expected output: 10
```

## 3. Rest 파라미터는 배열로만 해체할 수 있다.

```jsx
function f(...[a, b, c]) {
  return a + b + c;
}

f(1)          // NaN (b 와 c 는 undefined)
f(1, 2, 3)    // 6
f(1, 2, 3, 4) // 6 (4번 째 파라미터는 해체되지 않음)
```

## 4. 객체 구조 분해에서 Rest

객체 구조 분해에서 rest 속성은 구조 분해 패턴으로 걸러 지지 않은 열거형 속성의 키를 가진 나머지 항목을 모은다.

```jsx
const obj = { foo:1, bar: 2, baz: 3 };
const { foo, ...rest } = obj;
```

rest operator는 최대 한번만 사용 가능하며 객체 리터럴의 마지막에 나타나야 한다.

```jsx
const {...rest, foo} = obj; // SyntaxError
const {foo, ...rest1, ...rest2} = obj; // SyntaxError
```

단, 중첩 구조 분해 할당을 할 경우에는 rest 연산자가 여러 번 나올 수 있다.

```jsx
const obj = {
    foo: {
        a: 1,
        b: 2,
        c: 3,
    },
    bar: 4,
    baz: 5,
};
const {foo: {a, ...rest1}, ...rest2} = obj;

// Same as:
// const a = 1;
// const rest1 = {b: 2, c: 3};
// const rest2 = {bar: 4, baz: 5};
```

# Spread Syntax

위에서는 파라미터 리스트에서 배열을 얻었다. 그럼 반대로 배열을 분해하여 리스트를 얻을 수도 있을까? spread 연산자를 사용하면 가능하다.

## 1. 스프레드 연산자로 두 배열을 합쳐보기

```jsx
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];

let merged = [0, ...arr, 2, ...arr2]; // [0, 3, 5, 1, 2, 8, 9, 15];
```

## 2. 객체 리터럴에서 스프레드 연산자 사용하기

객체 리터럴에서 스프레드 연산자는 객체의 모든 열거 가능한 속성들을 객체 리터럴에 넣는다.

```jsx
const obj = {foo: 1, bar: 2, baz: 3};
const newObj = {...obj, qux: 4}; // {foo: 1, bar: 2, baz: 3, qux: 4}
```

키가 충돌되면 어떻게 될까? 나중에 나온 value 값이 키 값이 된다.

```jsx
const obj = {foo: 1, bar: 2, baz: 3};
const newObj = { ...obj, foo: true }; //{foo: true, bar: 2, baz: 3}
const newObj2 = { foo: true, ...obj }; // {foo: 1, bar: 2, baz: 3}
```

## 3. `Math.max`의 인자로 배열의 값들을 전달하고 싶을 때

```jsx
let arr = [3, 5, 1];
console.log(Math.max(arr)); // NaN
console.log(Math.max(arr[0], arr[1], arr[2]); // 5
```

배열의 요소가 많아질 때마다 arr[3], arr[4] ... 호출하는 건 좋은 코드가 아닐 거다. 이럴 때 스프레드 연산자를 사용한다. 스프레드 연산자는 배열을 분해하여 인자로 보낸다.

```jsx
let arr = [3, 5, 1];
console.log(Math.max(...arr)); // 5
```

## 4. iterable 객체는 구조 분해 될 수 있다.

대표적으로 String 객체를 구조 분해한 예제를 살펴보자. 

```jsx
let str = "Hello";
console.log([...str]); // [H, e, l, l, o]
console.log(Array.from(str)) // [H, e, l, l, o]
```

1. 이 코드는 스프레드 연산자를 이용하여 str(String 객체 === iterable 객체)를 구조 분해한 후 배열을 초기화 하는 작업을 한다. 스프레드 연산자는 내부적으로 `iterator`을 사용하기 때문에 `for...of` 와 동작이 같다.
2. 위 결과를 보면 `Array.from(str)`의 결과와 `[...str]`의 결과가 같다는 것을 알 수 있다. 하지만 작은 차이점이 있다. 스프레드 연산자는 오직 `iterable` 객체에만 적용될 수 있는 반면 `Array.from`은 `iterable` 객체 뿐만 아니라 배열 형태의 객체 에도 적용할 수 있다. 이러한 특성 때문에 `Array.from`을 더 선호하는 경향이 있다.

# 정리

`...` 는 Rest 파라미터 또는 Spread 구문 일 수 있다.

Rest Parameter

- 함수의 마지막 인자에 `...`를 사용한다.
- 함수의 인자들을 하나의 배열로 모은다.
- 인자의 개수가 유동적인 함수를 만들 때 주로 사용한다.

Spread syntax

- `...` 를 함수를 호출할 때나 그 밖에 사용할 경우
- 배열을 리스트로 분해하는 용도로 사용된다.
- 일반적으로 많은 인자들을 요구하는 함수에 배열을 보낼 때 사용한다.

참고

[Rest parameters and spread syntax](https://javascript.info/rest-parameters-spread)
