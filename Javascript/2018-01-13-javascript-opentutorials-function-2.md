# [생활코딩] javascript 함수지향2

- Title : [생활코딩] javascript 함수지향2
- Date : 2018-01-13
- Category: Javascript

생활코딩 함수지향 파트 중 값으로서의 함수와 콜백에 대한 내용을 정리했습니다.

# 1. 값으로서 함수

예제1) 자바스크립트에서는 함수도 객체이며, 함수도 값이 될 수 있다.

```javascript
function a() {} //함수는 a라는 변수에 담겨진 값.
var a = function() {};
```

예제2) 함수는 객체 안에 저장될 수 있다.

```javascript
a = {
  b: function() {}
};
```

b는 key, 객체 안에서 변수 역할(어떤 값을 저장하고 있기 때문) = 속성(property)  
속성에 저장된 값이 함수면 메소드라 부름.  
즉, a라는 객체에 b라는 속성이 있는데, 이 속성의 값은 함수(메소드)다.

예제3) 함수는 변수, 객체에 저장될 수 있고 인자로도 사용할 수 있다.

```javascript
function cal (func, num){
   return func(num)
}

function increase (num){
   return num+1;
}
alert (cal (increase, 1) );

// 위 코드는 아래와 같이 해석될 수 있다.
function cal ( func, num){
   var func = increase (num){
       return num+1
   }
}
// 즉, func(1) => 2가 출력됨.
```

예제4) 함수를 리턴 값으로도 사용할 수 있다.

```javascript
function cal (mode){
 val funcs = {
  'plus' : function (left, right) { return left + right },
  'minus' : function (left, right) { return left - right }
 }
 return funcs[mode];
}
alert (cal('plus')(2,1));
```

return funcs['plus'] 는 funcs 객체의 plus 속성의 값인 function(left, right) {return left+right} 메소드를 리턴시킨다.  
cal('plus')(2,1)은 cal('plus')함수의 인자 값으로 2, 1을 전달하겠다는 의미이다.  
cal('plus')는 function(left, right) {return left+right} 이므로 알럿창에 3이 뜬다.

예제5) 함수를 배열의 값으로 사용할 수 있다.

```javascript
var process = [
  function(input) {
    return input + 10;
  },
  function(input) {
    return input * input;
  },
  function(input) {
    return input / 2;
  }
];
var input = 1;
for (var i = 0; i < process.length; i++) input = process[i](input);

alert(input); //60.5
```

# 2. 콜백

```javascript
var numbers = [20, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1];
numbers.sort(); // numbers는 배열객체, sort()는 메소드(내장메소드)
console.log(numbers); // 1, 10, 2, 20, 3, 4, 5, 6, 7, 8, 9 - 문자 기준으로 정렬한 듯.
```

<span class="clr-grey">
sort()    
array.sort (sortfunc)    
반환값 : 정렬된 배열
</span>

```javascript
var numbers = [20, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1];
var sortfunc = function(a, b) {
  if (a > b) return 1;
  else if (a < b) return -1;
  else return 0;
};
var sortfunc = function(a, b) {
  return a - b; //역순은 return b-a;
};
numbers.sort(sortfunc); // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 20
```

sortfunc가 콜백함수가 된다.  
함수는 값으로서 사용할 수 있기 때문에 sortfunc를 sort에 인자로 전달함으로서 sort 메소드를 사용자가 원하는 방식으로 바꿀 수 있다.

<span class="clr-note">\*\*콜백은 비동기처리에서 유용하게 사용된다.</span>
