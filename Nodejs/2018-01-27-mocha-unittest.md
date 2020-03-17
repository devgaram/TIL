---
layout: post-content
title: (mocha) 자바스크립트 단위테스트 시작하기
date: 2018-01-27
tags: [mocha, javascript]
category: [javascript]
---

mocha 자바스크립트 테스트 프레임워크로 단위테스트 시작하는 방법입니다.

---

# 단위테스트(Unit Test)가 필요한 이유?
프로그램을 작은 단위로 쪼개서 내가 짠 프로그램이 내가 원하는 결과대로 동작하는 지 테스트할 수 있다.    
단위테스트를 거치면 프로그램의 안정성이 높아지며, 단위테스트를 믿고 리팩토링을 할 수 있다.    

자바스크립트로 짜여진 프로그램에서 오류를 찾을 때, 단위테스트를 거치지 않은 프로그램의 경우는 전체 흐름을 파악해가며 원인을 찾아야한다.     
원인을 찾아 수정한다해도 이 결과가 어떤 이펙트를 가져올지 예상하기가 어려울 수 있다.


# mocha 자바스크립트 테스트 프레임워크

## 1. 셋팅하기 (Windows8)
mocha는 node.js가 기반인 프레임워크이기 때문에 node.js가 설치되어있어야 한다.    
설치 후 cmd 창에 node --version 이라고 쳐서 버전 정보가 제대로 나오면 설치가 잘 된 것이다.

cmd 창에 아래와 같이 입력하면 mocha 사용 준비 완료!
```
$npm install -g mocha
```

그리고 Assertion Library인 Chai를 추가해보자. 작업 디렉토리에서 아래를 cmd 창에서 실행한다.
```
$nbm install chai
```
그 결과, node_moduls 폴더가 생성된다.


## 2. 사용하기
[InspiredJW님의 티스토리 블로그를 참고했습니다!](http://inspiredjw.com/entry/Mocha-%EB%A1%9C-%ED%95%98%EB%8A%94-JavaScript-Testing).    


모카는 test 디렉토리에 있는 파일을 실행하기 때문에, 테스트 코드는 이 디렉토리 안에 위치시켜야한다.

JsStudy/JsStudy.js 
```javascript
'use strict';
exports.foo = (a,b) => {
	if(typeof a !== 'number' || typeof b !== 'number')
		return false;
	return a*b;
};
exports.bar = (callback) => {};
```

JsStudy/test/JsStudyTest.js
```javascript
'use strict';

let chai = require('chai');
let should = chai.should();
let foo = require('../JsStudy').foo;

describe('Function Foo', () =>{
	describe('with two number params',()=>{
		it('should return product',()=>{
			let result = foo(3,4);
			result.should.be.a('number');
			result.should.equal(12);
		});
	});
});

describe('with non-number params',()=>{
	it('should return false',()=>{
		let result = foo(3,null);
		result.should.be.false;
	});
});
```

테스트 실행방법은 cmd 창에서 JsStudy 폴더로 이동한후, mocha 실행하기
```
$mocha
```

<span class="clr-grey">
API    
- describe() : 테이스틔 이름을 지정하며, 중첩하여 사용할 수 있다. 테스트를 구분하는 section이며, 단위테스트 부분을 콜백으로 넘겨준다.
- it() : 단위테스트의 이름을 작성하고 실제 실행되는 코드를 콜백(done)으로 넘겨준다.
- befor() : 테스트 코드가 실행되기 전 한번 실행된다.
- after() : 모든 테스트 코드가 실행된 후 한 번 실행된다.
- beforeEach() : (테스트 코드가 여러개일 경우) 각각의 테스트 코드가 실행되기 전마다 실행된다.
- afterEach() : (테스트코드가 여러개일 경우) 각각의 테스트 코드가 실행된 후마다 실행된다.
- .a(type[,msg]) : 지정된 타입과 동일한지 리턴.
- .equal(val[,msg]) : === var과 주어진 타켓이 같은지.
- .false : 타켓 === false
</span>