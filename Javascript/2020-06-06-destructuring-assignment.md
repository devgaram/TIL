# Destructuring assignment

- Title : destructuring assignment
- Date : 2020-06-06
- Category : Javascript

# 구조 분해 할당

## 1. 배열 구조 분해 할당

```jsx
let arr = ['garam', 'lee']

// let firstName = arr[0]
// let lastName = arr[1]
// 위와 같은 작업을 구조 분해 할당으로 간단히 할 수 있다.

let [firstName, lastName] = arr;
```

`split` 메소드처럼 배열을 리턴하는 함수를 사용할 때 구조 분해 할당을 사용하면 좋다.

```jsx
let [firstName, lastName] = 'garam lee'.split(' ');
```

### 1) 원본 배열에 영향을 주지 않는다.

구조 분해 할당은 원본 배열을 복사해서 각 변수에 할당하여 원본 배열에 영향이 없다.

```jsx
let arr = ['garam', 'lee'];
let [firstName, lastName] = arr;
console.log(arr); // ["garam", "lee"]
```

### 2) `,`를 사용하여 배열의 요소를 생략할 수 있다.

```jsx
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
console.log(firstName, title); // Julius Consul
```

### 3) 배열이 아닌 **iterable 가능한 객체**들도 구조 분해 할당을 할 수 있다.

```jsx
let [a, b, c] = "abc"; // ["a", "b", "c"];
let [one, two, three] = new Set([1, 2, 3]); // [1, 2, 3]
```

### 4) 객체의 속성에도 할당을 할 수 있다.

```jsx
let user = {};
[user.firstName, user.lastName] = "garam lee".split(" ");
console.log(user); // {firstName: "garam", lastName: "lee"}
```

### 5) 객체의 key-value 로 루프를 돌릴 때 구조 분해 할당을 함께 사용할 수 있다.

```jsx
let user = {
  name: "John",
  age: 30
};

for (let [key, value] of Object.entries(user)) {
  console.log(`${key}:${value}`); // name:John, then age:30
}

let user2 = new Map();
user2.set("name", "John");
user2.set("age", "30");

for (let [key, value] of user2) {
  console.log(`${key}:${value}`); // name:John, then age:30
}
```

### 6) 변수를 SWAP할 때 사용할 수 있다.

두 변수에 대한 배열을 생성한 후에 구조 분해 할당을 하기 때문에 swap이 가능하다.

```jsx
let guest = "Jane";
let admin = "Pete";

[guest, admin] = [admin, guest];
console.log(guest, admin); // Pete Jane
```

### 7)  `...` 를 사용하여 배열의 남은 요소를 할당할 수 있다.

```jsx
let [name1, name2, ...rest] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
console.log(name1, name2); // Julius Caesar
console.log(rest, rest.length); // ["Consul", "of the Roman Republic"] 2
```

### 8) default value 할당하기

할당될 값이 없을 때 default value로 설정한 값이 변수에 할당된다.

```jsx
let [firstName, surname] = []; // undefined, undefined
let [firstName = "Guest", surname = "Anonymous"] = []; // Guest, Anonymous
```

## 2. 객체 구조 분해 할당

```jsx
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

let {title, width, height} = options;
```

### 1) `{ 객체의 프로퍼티: 변수 이름 }` 과 같은 형식으로 변수 이름을 변경할 수 있다.

```jsx
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

let {width: w, height: h, title} = options;
console.log(w, h, title); // 100, 200, "Menu"
```

### 2) `=` 을 이용하여 default value 할당할 수 있다.

```jsx
let options = {
  title: "Menu"
};

let {width = 100, height = 200, title} = options;
console.log(width, height, title); // 100, 200, "Menu"

// 이름 변경도 동시에 가능
let {width: w = 100, height: h = 200, title} = options;
```

### 3) rest 패턴을 사용할 수 있다.

```jsx
let options = {
  title: "Menu",
  height: 200,
  width: 100
};

let {title, ...rest} = options;

console.log(title, rest.height, rest.width); // "Menu" 200 100
```

## 3. 중첩 구조 분해

```jsx
let options = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Cake", "Donut"],
  extra: true
};

let { size: { width, height}, items: [item1, item2], title = "Menu" } = options
/*
 size, items: is not defined
 width: 100
 height: 200
 item1: Cake
 item2: Donut
 title: Menu
*/

```

## 4. 함수의 파라미타에 구조 분해 할당을 사용하자

아래 함수의 경우는 파라미타의 순서를 기억 해야 한다는 단점이 있다. 더 많은 파라미타를 다뤄야 한다면..?

```jsx
function showMenu(title = "Untitled", width = 200, height = 100, items = []) {
  // ...
}
showMenu("My Menu", undefined, undefined, ["Item1", "Item2"])
```

이 때 구조 분해 할당을 사용하면 좋다.

```jsx
let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

function showMenu({title = "Untitled", width = 200, height = 100, items = []}) {
  alert( `${title} ${width} ${height}` ); // My Menu 200 100
  alert( items ); // Item1, Item2
}

showMenu(options);
```

만약 파라미타로 아무 값도 넘기고 싶지 않다면 아래와 같이 코드를 작성하면 된다.

```jsx
showMenu({}); // 빈 객체를 보내면 된다. 하지만, 정말 아무 것도 보내고 싶지않다면?
showMenu(); // Error 발생
```

전체 argument 객체의 디폴트 객체로 {} 를 할당하면 에러를 잡을 수 있다.

```jsx
function showMenu({title = "Untitled", width = 200, height = 100, items = []} = {}) {
  alert( `${title} ${width} ${height}` ); // My Menu 200 100
  alert( items ); // Item1, Item2
}

showMenu(); // 이제 에러가 발생하지 않는다.
```

참고
[Destructuring assignment](https://javascript.info/destructuring-assignment)
