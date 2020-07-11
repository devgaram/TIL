# 자바스크립트 엔진과 이벤트 루프

Date: Jul 1, 2020

- Title : 자바스크립트 엔진과 이벤트 루프
- Date : 2020-07-01
- Category : Javascript

![Untitled](https://user-images.githubusercontent.com/34448538/87217780-f49aac00-c387-11ea-9c33-5a77863c7f28.png)

## V8 엔진

자바 스크립트 엔진 중 하나로 구글이 개발한 오픈 소스다. C++로 개발되어 있으며 크롬과 NodeJS에서 사용된다.

**V8 엔진은 크게 두 부분으로 구성된다.**

- 메모리 힙 (Memory Heap) : 메모리 할당이 이루어지는 곳
- 콜 스택 (Call Stack) : 코드가 실행 되면서 스택 프레임이 쌓이는 곳

### 메모리 힙

객체나 동적 데이터(배열, 객체...)가 저장되는 공간으로 메모리 영역에서 가장 큰 블록이면서 가비지 컬렉션(GC)가 발생하는 곳이다. 힙 메모리 전체에서 가비지 컬렉션이 실행되는 것은 아니고 Young과 Old 영역에서만 실행된다.

### 콜 스택

메서드, 함수 프레임, 원시 값, 객체 포인터를 포함한 정적 데이터가 저장되는 곳이다.
자바 스크립트는 콜 스택을 하나만 가지고 있는 싱글 스레드 언어이다.

> **스택 트레이스?**
예외가 발생했을 때의 콜 스택의 상태
**스택 날림(Blowing the stack)?**
스택 프레임이 콜 스택의 최대 크기까지 쌓였을 때

### V8 메모리 사용 (스택 vs 힙)

```jsx
class Employee {
    constructor(name, salary, sales) {
        this.name = name;
        this.salary = salary;
        this.sales = sales;
    }
}

const BONUS_PERCENTAGE = 10;

function getBonusPercentage(salary) {
    const percentage = (salary * BONUS_PERCENTAGE) / 100;
    return percentage;
}

function findEmployeeBonus(salary, noOfSales) {
    const bonusPercentage = getBonusPercentage(salary);
    const bonus = bonusPercentage * noOfSales;
    return bonus;
}

let john = new Employee("John", 5000, 5);
john.bonus = findEmployeeBonus(john.salary, john.sales);
console.log(john.bonus);
```

![stackheap](https://raw.githubusercontent.com/devgaram/TIL/master/Javascript/images/2020-07-01-img/stack-heap.gif)

# 이벤트 루프와 비동기 프로그래밍

자바 스크립트는 스레드(콜 스택이 하나)가 하나라 수행 시간이 긴 코드를 실행하게 되면 브라우저는 아무 것도 할 수 없게 된다. 하지만 실제 자바 스크립트가 사용 되는 환경을 생각해보면 많은 작업이 동시에 처리되고 있다. 예를 들면, 웹 브라우저는 애니메이션 효과를 보여주면서 마우스 입력을 받아서 처리하고, NodeJs 기반의 웹 서버에서는 동시에 여러 개의 HTTP 요청을 처리 하기도 한다. 

싱글 스레드인데 어떻게 이런 일이 가능할까? 자바 스크립트는 어떻게 동시성(Concurrency)을 지원할까? 이에 대한 답은 `자바 스크립트는 이벤트 루프 기반의 비동기 방식으로 동시성을 지원 한다`로 정리할 수 있다.

## Web APIs

브라우저가 제공하는 웹 API로 DOM Events, XMLHttpRequest, setTimeout, addEventListener 등이 포함된다.

## 이벤트 루프와 태스크 큐

Web API 중 하나인 `setTimeout` 함수는 태스크 큐에서 대기하고 있다가 이벤트 루프를 통해 실행된다.
태스크 큐는 말 그대로 콜백 함수들이 대기하는 큐(FIFO) 형태의 배열이라 할 수 있고 이벤트 루프는 콜 스택이 비어질 때마다 큐에서 콜백 함수를 꺼내 와서 실행하는 역할을 한다.

```jsx
setTimeout(baz, 10); // (1)
foo();
```

위의 예시의 코드가 처음 실행되면 `setTimeout` 함수는 브라우저에게 타이머 이벤트를 요청한 후 바로 콜 스택에서 제거된다. 그 후에 `foo` 함수가 콜 스택에 추가된다. 10ms가 지나면 브라우저의 타이머는 `baz` 함수를 태스크 큐에 추가한다. 이벤트 루프는 `foo` 함수가 실행을 마치고 콜 스택에서 사라지면 태스크 큐에서 대기 중인 `baz`를 실행하여 콜 스택에 추가한다.

![image](https://user-images.githubusercontent.com/34448538/87217762-c74dfe00-c387-11ea-920e-87d0761c4fff.png)

정리하자면 이벤트 루프는 **현재 실행 중인 태스크가 없는 지**와 **태스크 큐에 태스크가 있는지**를 반복적으로 확인하여 **현재 실행 중인 태스크가 없을 때**(주로 호출 스택이 비워졌을 때) 태스크 큐의 첫번째 태스크를 가져와 실행한다. 참고로 모든 브라우저의 비동기 API들은 작업이 완료되면 콜백 함수를 태스크 큐에 추가한다.

### 마이크로 태스크

일반 태스크보다 더 높은 우선 순위를 갖는 태스크로 태스크 큐에 대기 중인 태스크가 있더라도 마이크로 태스크가 먼저 실행된다. 

```jsx
setTimeout(function() { // (A)
    console.log('A');
}, 0);
Promise.resolve().then(function() { // (B)
    console.log('B');
}).then(function() { // (C)
    console.log('C');
});
```

Promise 콜백 함수는 마이크로 태스크 큐에 추가되고 setTimeout 콜백 함수는 일반 태스크 큐에 추가되기에 이벤트 루프는 마이크로 태스크 큐에 있는 작업을 먼저 실행한 후 태스크 큐의 태스크를 실행한다. 즉 B → C → A 순으로 실행된다.

 
참고
[https://vimeo.com/96425312](https://vimeo.com/96425312)

[https://meetup.toast.com/posts/89](https://meetup.toast.com/posts/89)

[https://engineering.huiseoul.com/자바스크립트는-어떻게-작동하는가-v8-엔진의-내부-최적화된-코드를-작성을-위한-다섯-가지-팁-6c6f9832c1d9](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-v8-%EC%97%94%EC%A7%84%EC%9D%98-%EB%82%B4%EB%B6%80-%EC%B5%9C%EC%A0%81%ED%99%94%EB%90%9C-%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%9E%91%EC%84%B1%EC%9D%84-%EC%9C%84%ED%95%9C-%EB%8B%A4%EC%84%AF-%EA%B0%80%EC%A7%80-%ED%8C%81-6c6f9832c1d9)
