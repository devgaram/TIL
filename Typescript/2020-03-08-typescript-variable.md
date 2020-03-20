# [타입스크립트] 변수 선언과 타입

- Title : [타입스크립트] 변수 선언과 타입
- Date : 2020-03-08
- Category: Typescript

## 1. 변수 선언

**var**

- 호이스팅
- 함수 레벨 스코프

**let**

- 변수를 초기화하기 전에는 변수에 접근할 수 없게 호이스팅 방지
- 같은 블록 내에서 같은 이름의 변수 중복 선언 불가
- 블록 레벨 스코프

**const**

- 호이스팅 안함
- 블록 레벨 스코프
- 변수를 상수로 선언할 때 사용(재할당되지 않는 읽기 전용 변수)
- 객체로 초기화하는 경우 값 자체를 재할당할 수는 없으나 속성값의 변경은 허용

> 호이스팅 - 선언한 변수가 스코프의 최상위로 끌어올림 되는 현상

## 2. 타입 검사

**정적 타입 검사**

- 컴파일 타임에 타입 검사
- 타입 검사하는 데 시간은 들지만 안정성 보장
- 자바, C++

**동적 타입 검사**

- 실행 시간에 타입 검사 수행
- 동적 타이핑 수행
  - 동적 타이핑 : 값을 변수에 할당할 때 타입이 정해지는 것
- 자바스크립트

**점진적 타입 검사**

- 컴파일 시간에 타입 검사를 수행
- 타입 선언 생략 허용
  - 생략시 암시적 형변환 발생
  - 암시적 형변환 : 컴파일러가 알아서 데이터 타입 변환
- 여러 타입을 결합해 새로운 타입을 정의하는 것 허용
- 타입스크립트, 파이썬

## 3. 타입 계층도

- 타입스크립트는 자바스크립트에서 지원하는 내장 타입을 제공하며 크게 기본 타입, 객체 타입, 함수 타입이 있다.

![200308/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Typescript/images/2020-03-08-img/Untitled.png)

## 4. Primitive(기본) 타입

### 1) string, number, boolean 타입

```ts
// string
let name: string = "rami"; // 큰 따옴표 사용 권장
let sentence: string = `안녕하세요 ${name}입니다.`;

// number
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;

// boolean
let isShow: boolean = false;
```

### 2) enum 열거 타입

- numberic 값 집합에 친숙한 이름을 부여하는 방법
- 기본적으로 멤버 번호는 0부터 시작
- 수동으로 값을 변경할 수도 있음

```ts
enum Color {
  Red,
  Green,
  Blue
}
let c: Color = Color.Green;
console.log(c); // 1
console.log(typeof Color); // object

// 0대신 1로 시작할 수 있게 --> 1, 2, 3
enum Color {
  Red = 1,
  Green,
  Blue
}
let c: Color = Color.Green;

// 열거 형 모든 값 수동 설정
enum Color {
  Red = 1,
  Green = 2,
  Blue = 4
}
let c: Color = Color.Green;

// 2가 Color enum의 어떤 값과 매핑되는 지 모를 때 이름을 찾을 수 있음
enum Color {
  Red = 1,
  Green,
  Blue
}
let colorName: string = Color[2];
consolo.log(colorName); // Green
```

**const enum**

- 일반 열거형과 달리 컴파일하는 동안 완전히 제거됨

```ts
// typescript
enum WeekDay {
  Mon = 1,
  Tue,
  Wed,
  Thu
}
console.log(WeekDay.Mon); // 1
console.log(WeekDay["Mon"]); // 1
console.log(WeekDay[WeekDay.Mon]); // Mon
let day = WeekDay;
// enum 객체의 키는 속성의 인덱스, 속성의 이름이 된다.
// 따라서 객체에 할당되는 속성의 수는 enum 객체의 속성 개수보다 2배 많다.
console.log(day); // {1: "Mon", 2: "Tue", 3: "Wed", 4: "Thu", Mon: 1, Tue: 2, Wed: 3, Thu: 4}

// 컴파일 후 ES5
"use strict";
var WeekDay;
(function(WeekDay) {
  WeekDay[(WeekDay["Mon"] = 1)] = "Mon";
  WeekDay[(WeekDay["Tue"] = 2)] = "Tue";
  WeekDay[(WeekDay["Wed"] = 3)] = "Wed";
  WeekDay[(WeekDay["Thu"] = 4)] = "Thu";
})(WeekDay || (WeekDay = {}));
```

```ts
// typescript
const enum WeekDay {
  Mon = 1,
  Tue,
  Wed,
  Thu
}
console.log(WeekDay.Mon); // 1
console.log(WeekDay["Mon"]); // 1
console.log(WeekDay[WeekDay.Mon]); // 에러, 문자열 리터럴을 이용해서만 접근 가능
let day = WeekDay; // 에러, 속성 또는 인덱스 표현식으로만 가능
// 컴파일 후 ES5
"use strict";
```

**기타 enum**

```ts
// 초기값으로 문자열 할당 가능
enum WeekDay {
  Mon = "MON",
  Tue = "TUE",
  Wed = "WED",
  Thu = "TUE"
}
console.log(WeekDay.Mon); // MON
console.log(WeekDay["Mon"]); // MON
// 리버스 매핑 적용 안됨
// 문자열 할당하면 속성 값으로 속성 이름 알 수 없음
console.log(WeekDay[WeekDay.Mon]); // 에러,

// 문자열 또는 숫자 섞어서 가능
enum WeekDay {
  Mon = "MON",
  Tue = 1,
  Wed = "WED",
  Thu = "TUE"
}

// boolean, 표현식 불가능
enum WeekDay {
  Mon = "MON",
  Tue = 1,
  Wed = true,
  Thu = "TUE"
}
enum WeekDay {
  Mon = "MON",
  Tue = 1,
  Wed = 1 + 2,
  Thu = "TUE"
}
```

### 3) symbol 타입

- Symbol 생성자를 호출하여 생성
- 불변 고유
- 심벌 객체는 호출될 때마다 새로운 심벌 객체를 만듬
- 객체 리터럴의 속성 키로 사용할 수 있음

```ts
let hello = Symbol("key");
let hello2 = Symbol("key");
console.log(hello === hello2); // false
console.log(hello, hello2); // Symbol(key) Symbol(key)
console.log(typeof hello); // symbol

let uniqueKey = Symbol();
let obj = {
  [uniqueKey]: 1234,
  title: "hi"
};
console.log(obj.uniqueKey); // undefined
console.log(obj.title); // hi
console.log(obj); // {title: "hi", Symbol(): 1234}
```

### 4) string literal 타입

```ts
// 사용자 정의 타입에 정의한 문자열만 할당 받을 수 있음
type EventType = "keyup" | "mouseover";
```

## 5. Object(객체) 타입

### 1) Array(배열) 타입

- 타입스크립트에서 배열 타입은 배열 타입과 제네릭 배열 타입으로 나뉜다

```ts
let arr: string[] = ["a", "b", "c"];
let arr2: Array<string> = ["a", "b", "c"];
```

**배열 타입**

- 요소 타입으로 string, boolean, number와 같은 자바스크립트 내장 타입뿐 아니라 클래스나 인터페이스도 올 수 있음

```ts
let items: number[] = [1, 2, 3];
let fruits: string[] = [];
fruits.push("banana");
fruits.push("apple");

// any 타입 지정하면 어떤 타입이든 배열의 요소로 추가 가능
let myVar: any[] = [1, "hi", true];

// 유니언 타입
let myVar: (number | string | boolean)[] = [1, "hi", true];
```

**제네릭 배열**

- Array<T> 형태로 선언 (T: 타입)
- 제네릭 타입 인수로 사용된 타입은 컴파일 시점에 타입을 검사함

```ts
let num: Array<number> = [1, 2, 3];
let num: Array<number | string> = [1, "hello"];
let num2: typeof num = [1, "hello"]; // 타입 쿼리로 num 변수의 타입을 참조

// 배열요소로 익명 함수 받으려면
let nums: Array<() => string> = [() => "one", () => "two"];
console.log(nums[0]()); // one 출력

// 배열 선언 부분과 요소 추가하는 부분 분리
let num3: Array<number> = new Array<number>();
num3.push(1);
num3.push(2);
num4.push(3);
```

타입스크립트에서 배열 타입이든 제네릭 타입이든 컴파일 시 타입 검사를 위해 필요하며 컴파일 후(ES5)에는 타입이 제거된 배열만 남는다.

### 2) Tuple(튜플) 타입

- 배열 요소가 n개로 정해질 때 각 요소별로 타입을 지정함

```ts
let x: [string, number];
x = ["tuple", 100]; // 성공
x = [10, "tuple"]; // 에러
console.log(typeof x, typeof x[0], typeof x[1]); // object string number
console.log(x[0].substring(1)); // uple
console.log(x[1].substring(1)); // 에러
x[3] = "world"; // 에러 - [string, number] 타입에서 3번째 속성이 없어서
```

Function, 생성자, Class, Interface는 추후 정리

## 6. Union 타입

2개 이상의 타입을 하나의 타입으로 정의

```ts
let x: string | number;
```

## 7. Intersection 타입

두 타입을 합쳐 하나로 만들 수 있는 타입

```ts
interface Cat {
  leg: number;
}
interface Bird {
  wing: number;
}
let birdCat: Cat & Bird = { leg: 4, wing: 2 };
```

## 8. Void 타입

- 빈 값을 나타내는 타입
- 함수의 반환값이 없을 때
- null이나 undefined만 할당 가능
  - void 타입이 null, undefined의 상위 타입이기 때문

```ts
// 반환값이 없는 함수
function say(): void {
  alert("hello!");
}
// 반환값은 void이지만 실제로는 undefined가 할당됨
let mySay: void = say();

// 변수에 undefined나 null 할당 -> 비추
let unusable: void = undefined;
let unusable; // 위 코드와 같은 의미
let unusable = null; // 쓰지 말자
```

> 변수를 선언할 때 값을 할당하지 않았음을 나타내기 위해 선언한 변수에 null을 할당하는 것은 권장하지 않음

- null이나 undefined를 할당하면 컴파일러 오류가 발생하도록 tsconfig.json에 아래와 같이 설정한다.

```ts
    {
    	"compilerOptions": {
    		"strictNullChecks": true
    	}
    }
```

## 9. null과 undefined 타입

- 둘다 모든 타입의 하위타입
- undefined는 선언은 됐지만 값이 할당되지 않은 것
- null은 값은 할당됐지만 값이 빈 객체로 초기화되는 것

```ts
let person = { name: "happy" };
person = null;
console.log(typeof person); // object

// undefined vs null
let testUndefined;
console.log(testUndefined, typeof testUndefined); // undefined 'undefined'

let testUndefined2: undefined = undefined;
console.log(testUndefined2, typeof testUndefined2); // undefined 'undefined'

let testNull: null = null;
console.log(testNull, typeof testNull); // null 'object'

// 비교
console.log(undefined === null); // false
console.log(undefined == null); // true
console.log(undefined == undefined); // true
```

## 10. Any 타입

- 제약이 없는 타입으로 어떤 타입의 값도 받아들일 수 있음
- 최소한의 정적 타입 검사만 수행
- 외부라이브러리의 연산 결과를 받는 것과 같이 타입 결과를 예측할 수 없을 때
- 배열 값의 타입이 다양해서 한 가지 타입으로 고정할 수 없을 때

```ts
let basket: any = 10;
basket = true;
basket = "banana";
console.log(basket);

let vList: any[] = [1, false, "happy"];
console.log(vList[0]);
```

### 1) **최소한의 정적 타입 검사...?**

- any 타입으로 선언된 변수는 최소한의 정적 타입 검사만 수행

아래 예를 보면 any 타입으로 선언한 경우, 존재하지않는 함수를 호출할 때 컴파일 에러는 안나지만 런타임 에러가 난다. 속성의 유무를 런타임 시에 검사하나보다..

```ts
let number = 50;
let number2: any = 50;

console.log(number.toFixed(2)); // 50.00
console.log(number2.toFixed(2)); // 50.00

number.notExistMethod(); // 컴파일 타임에 에러
number2.notExistMethod(); // 컴파일 에러는 안나지만 런타임 에러
```

### 2) object 타입과의 차이...?

object도 any 처럼 타입 구분 없이 값을 할당할 수 있는 특성이 있다. 하지만 속성 유무를 검사하는 시간이 다름. any는 런타임 시에 검사, object는 컴파일 시간에 속성 유무 검사

```ts
let number2: Object = 50;
// 변수에 할당된 타입이 number인지 string인지 몰라서 컴파일 에러 뱉음
number2.toFixed(1); // property 'toFixed' does not exist on type 'Object'
```

### 3) 비교

```ts
let number1 = 50;
let number2: any = 50;
let number3: Object = 50;
console.log(typeof number1); // number
console.log(typeof number2); // number
console.log(typeof number3); // number
console.log(number1.toFixed(2)); // 50.00
console.log(number2.toFixed(2)); // 50.00
console.log(number3.toFixed(2)); // 컴파일 에러, Object에 tofixed가 없음
```

### 4) noImplicitAny 옵션

타입을 생략하면 명시적으로 any 타입이 된다. any 타입임을 추론할 수는 있지만, 명시적으로 any 타입임을 선언하는 것이 더 명확하므로 any 타입을 강제하는 게 좋다. 이는 tsconfig.json 파일에서 noImplicitAny 속성을 true로 하면 된다.
