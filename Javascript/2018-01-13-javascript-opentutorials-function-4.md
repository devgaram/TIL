# [생활코딩] javascript 함수지향4

- Title : [생활코딩] javascript 함수지향4
- Date : 2018-01-13
- Category: Javascript

생활코딩 함수지향 파트 중 arguments와 apply()에 대한 내용을 정리했습니다.

# 1. arguments

```javascript
function sum(){
    var i, _sum = 0;
    for( i=0; i< arguments.length; i++){
        document.write(i +  ":" + arguments[i] + <'br/>');
        _sum += arguments[i];
    }
    return _sum;
}
document.write('result : ' + sum(1,2,3,4));
```

arguments는 사용자가 전달한 인자들이 담겨있는 객체다.

- .length를 통해 함수가 몇개의 인자 전달 받았는 지를 알 수 있다.
- arguments[i] 를 통해 각 자리의 인자 값을 알 수 있다.

자바스크립트는 매개변수의 수가 함수의 정의와 달라도 에러가 발생하지 않는다.

```javascript
function one(arg1) {
  console.log(
    "one.length",
    one.length, //1
    "arguments",
    arguments.length //2
  );
}
one("val1", "val2");
```

위의 차이점을 이용해보면. 함수에 정의된 매개변수의 갯수와 실제 전달받는 인자의 갯수를 비교해서 대상 함수가 올바르게 사용되었는 지를 체크 할 수 있다.

# 2. apply() 내장함수

```javascript
function sum(arg1, arg2) {
  return arg1 + arg2;
}
sum(1, 2); //3
sum.apply; //function apply(){[native code]}     native code : 내장함수란 뜻
sum.apply(null, [1, 2]); //3     -첫번째 인자가 null로 들어갈 때는, 쓰지않기.
```

왜 사용할까?

```javascript
o1 = { val1: 1, val2: 2, val3: 3 };
o2 = { v1: 10, v2: 50, v3: 100, v4: 25 };
function sum() {
  var _sum = 0;
  for (name in this) {
    _sum += this[name];
  }
  return _sum;
}
alert(sum.apply(o1)); //6
alert(sum.apply(o2)); //185
```

o1 객체를 sum의 인자 값으로 전달한다.  
this는 var this = o1; 과 암시적으로 같다.  
실행되는 그 순간에는 o1이라는 객체의 메소드 sum이 된다는 것.  
즉, o1 = { va1 : 1, val2 : 2, val3 : 3, sum : sum} 에 o1.sum()과 같다.
