# Strict Mode

Date: Jun 13, 2020

- Title : Strict Mode
- Date : 2020-06-13
- Category : Javascript

# Strict mode

- Javscript ES5 이후로 권장하는 코딩 스타일 중 하나는 `Strict mode`를 사용하는 것이다.
- Javascript 언어의 문법을 보다 엄격히 적용하여 기존에는 **무시 되던 오류를 발생**시킬 가능성이 높거나 Javascript **엔진의 최적화 작업에 문제**를 일으킬 수 있는 코드에 대해 **명시적인 에러**를 발생시킨다.
- Strict mode는 ES5에 정의되어 있으며 브라우저가 Strict mode를 지원하지 않아도 실행하는 데 전혀 무관하니 Strict mode로 설정해서 개발하는 것이 좋다.
- Strict mode를 지원하지 않는 IE9 이전 버전에서는 문자열로 무시되고 일반 모드로 실행된다.

전역 strict mode 사용 방법(전역에 적용하는 것은 피하자!)

```javascript
"use strict";
```

파일 상단에 키워드를 넣으면 전체 소스 파일에 대해서 엄격 모드로 실행된다.

함수 내부에서 strict mode 사용 방법

```javascript
(function() {
	"use strict";
}());
```

즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

## 예시

var, const, let 없이 변수를 사용할 때 레퍼런스 오류 발생

```javascript
(function(){
	"use strict";
	x = 1;
	console.log(x); // Uncaught ReferenceError: x is not defined
}());
```

변수나 함수를 삭제하려고 하면 문법 오류 발생

```javascript
(function(){
	"use strict";
	let x = 1;
	function fun() {}

	delete x;
	delete fun;
	// SyntaxError: Delete of an unqualified identifier in strict mode.

}());
```

중복되는 파라미터명이 있을 때 문법 오류 발생

```javascript
(function(win, doc, doc){
	"use strict";
	console.log(doc); // SyntaxError: Duplicate parameter name not allowed in this context
}(window, document));
```

argument를 설정하려고 하면 문법 오류 발생

```javascript
(function(win, doc, doc){
	"use strict";
	arguments = [1, 2, 3];
	console.log(arguments); // SyntaxError: Unexpected eval or arguments in strict mode
}(window, document));
```
