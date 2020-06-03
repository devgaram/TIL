# 자바스크립트 async, await, promise

- Title : 자바스크립트 async, await, promise
- Date : 2020-05-20
- Category : Javascript

# Rules for Promise

1. 비 동기나 블로킹 코드 작업을 하고 싶을 때 `Promise`를 사용하자.
2. `resolve`는 `then`과  `reject`는  `catch` 와 맵핑된다.
3. `.catch` `.then` 메소드를 사용하자.
4. 꼭 실행 해야 할 코드가 있다면 `.finally` 를 사용하자.
5. 여러 개의 `Promise`가 모두 완료 되었을 때 작업을 진행하고 싶다면 `Promise.all(iterable)`를 사용하자. `Promise.all`은 배열 내 모든 프로미스의 이행(또는  첫 번째 거부)를 기다린다.

    ```jsx
    const p1 = Promise.resolve(3);
    const p2 = 1337;
    const p3 = new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("foo");
      }, 100);
    }); 

    // p2는 프로미스가 아니지만 이행 결과 배열에는 포함된다.
    Promise.all([p1, p2, p3]).then(values => { 
      console.log(values); // [3, 1337, "foo"] 
    });

    // 매개변수 배열이 빈 것과 동일하게 취급하므로 이행함
    const p4 = Promise.all([1,2,3]); // Promise {<resolved>: Array(3)} [1, 2, 3]

    // 프로미스 거부됨
    const p5 = Promise.all([1,2,3, Promise.reject(555)]); // Uncaught (in promise) 555
    ```

# Rules for async-await

1. `async function` 은 `Promise`를 리턴한다.
2. 코드에서 직접 `Promise`를 직접 리턴하지않았어도 async 함수는 Promise를 리턴한다는 것을 기억하자.
3. `await` 블록은 `async` 함수 안에 위치한다.
4. `async` 함수에는 두 개 이상의 `await` 구문을 사용할 수 있다.
5. `async await`를 사용할 때, `try~catch`로 에러 핸들링을 하자.
6. `iterator`과 `loop`에서 `await`를 사용하며 코드가 순차적으로 실행될 것 같지만 사실은 동시에 실행된다.
7. `await`는 하나의 `Promise`를 위해서만 사용하자.
8. `await` 블록은 항상 `async` 함수 내에 있다. `promise`가 `resolve`되면 다음 코드가 실행된다는 것을 기억하자. 

# Promise vs async-await

1. `async` 함수는 `promise`를 반환하고 반대도 성립된다. 즉, `promise`를 반환하는 모든 함수는 `async` 함수로 사용할 수 있다.
2. function2의 아웃풋이 function1의 아웃풋에 따라 달라진다면 `await`를 사용하자.
3. 두 개의 함수가 순차적으로 실행될 필요가 없다면 `await`를 쓸 필요가 없다.
4. Promise를 동시에 실행시키고 싶다면 `Promise.all`을 사용하자
5. `await`는 `blocking code`를 만든다는 사실을 잊지 말자.
6. 많은 await 구문이 있는 async 함수를 사용하는 것보다는 여러 개의 async 함수로 쪼개서 사용하자.
    - 너무 많은 `blocking code`는 좋지 않다.
    - 비동기 코드가 수평적으로 실행된다는 것을 강조할 수 있다.

[JavaScript: Promises or async-await](https://medium.com/better-programming/should-i-use-promises-or-async-await-126ab5c98789)

# Symbol

```jsx
// 위 코드는 매 번 새로운 심볼을 생성한다.
const sym1 = Symbol();
const sym2 = Symbol("foo");
const sym3 = Symbol("foo");
```

1. 이름 충돌의 위험 없이 **속성의 키**로 쓰기 위해 생성하고 사용할 수 있는 값이다.
2. Boolean, String, Number 같이 원시 데이터 형의 일종이다.
3. `Symbol()` 로 부터 반환되는 모든 심볼 값은 고유하다.
4. 객체 속성에 대한 `식별자`로 사용할 수 있다.
5. `Object.getOwnPropertySymbols()` 메서드를 이용하여 객체의 심볼 속성을 찾을 수 있다. 모든 객체는 심볼 속성이 없는 상태로 초기화 되기 때문에 해당 객체에 심볼 속성을 설정하기 전까지는 빈 배열을 반환한다는 점에 유의하자.
