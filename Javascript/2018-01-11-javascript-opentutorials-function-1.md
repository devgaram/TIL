# [생활코딩] javascript 함수지향1

- Title : [생활코딩] javascript 함수지향1
- Date : 2018-01-11
- Category: Javascript

생활코딩 함수지향 파트 중 변수의 유효범위에 대한 내용을 정리했습니다.

# 변수의 유효범위

예제1) 지역변수와 전역변수의 구분1

```javascript
var vscope = "global"; //전역변수
function fscope() {
  alert(vscope);
}
fscope(); //global
```

<span class="clr-grey">
**Note:** 전역변수 : 애플리케이션 전역에서 접근 가능한 변수    
**Note:** 지역변수 : 함수 내에서만 접근 가능한 변수    
*변수에 접근할 때, 지역변수를 우선적으로 찾는다.
</span>

</br>
예제2) 지역변수와 전역변수의 구분2
```javascript
var vscope = 'global';   
function fscope(){
   var vscope = 'local';    //지역변수
   var lv = 'local value';   //지역변수
   alert(vscope);
}
fscope();    //local
alert(lv);    //undefined
```

예제3) 지역변수와 전역변수의 구분3

```javascript
function a() {
  i = 0;
}
for (var i = 0; i < 5; i++) {
  // i는 전역변수
  a();
  document.write(i);
}
```

for문의 i는 전역변수기 때문에 해당 코드는 계속 실행되다가 브라우저가 멈춘다.

예제4) 전역변수 안쓰는 방법1

```javascript
var MYAPP = {};
MYAPP.calculator = {
  //객체의 속성(calculator)에도 객체를 생성.
  left: null,
  right: null
};
MYAPP.coordinate = {
  left: null,
  right: null
};

MYAPP.calculator.left = 10;
MYAPP.calculator.right = 20;
function sum() {
  return MYAPP.calculator.left + MYAPP.calculator.right;
}
document.write(sum()); //30
```

MYAPP 전역변수 하나만 생성하고, 그 안에 필요한 전역변수를 정의한다.

예제5) 전역변수 안쓰는 방법2

```javascript
(function() {
  var MYAPP = {};
  MYAPP.calculator = {
    //객체의 속성(calculator)에도 객체를 생성.
    left: null,
    right: null
  };
  MYAPP.coordinate = {
    left: null,
    right: null
  };

  MYAPP.calculator.left = 10;
  MYAPP.calculator.right = 20;
  function sum() {
    return MYAPP.calculator.left + MYAPP.calculator.right;
  }
  document.write(sum()); //30
})(); //익명함수
```

전역변수가 절대 존재하지 않게 개발할 때, 익명함수. 일회성함수를 이용한다.  
Jquery와 같은 라이브러리에서 모듈화 기법으로 많이 사용한다.

예제6) 자바와의 차이점. for문 안에서의 변수.

```javascript
for (var i = 0; i < 1; i++) {
  var name = "local no!";
}
alert(name); //local no!
```

자바는 반복문, 조건문 안에서 선언된 변수를 지역변수 취급하기 때문에 위와 같은 로직으로 실행시키면 에러가 난다.  
그러나 자바스크립트에서 해당 변수는 전역변수이다.

예제7) 정적 유효범위

```javascript
var i = 5;
function a() {
  var i = 10;
  b();
}
function b() {
  document.write(i); //누구의 i일까?
}
a(); //5가 출력된다!
```

함수에서 변수를 찾을 때, 함수 내 지역변수를 먼저 찾고. 전역변수를 찾는다.  
<span class="clr-note">
\*\*사용(호출)될 때가 아니라 정의될 때의 전역변수가 사용된다. -> 정적 유효범위
</span>
