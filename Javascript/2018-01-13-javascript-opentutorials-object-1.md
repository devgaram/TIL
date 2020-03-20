# [생활코딩] javascript 객체지향1

- Title : [생활코딩] javascript 객체지향1
- Date : 2018-01-13
- Category: Javascript

생활코딩 객체지향에 대한 내용을 정리했습니다.

객체(object)는 연관되어 있는 것(변수, 메소드)들을 그룹화한 것, 각 그룹화된 것은 독립성을 가지는 부품이 된다.

# 1. 객체의 생성 방법

this는 함수가 속해있는 객체인 person을 가리킨다.

```javascript
var person = {};
person.name = "egoing";
person.introduce = function() {
  return "My name is" + this.name;
};
document.write(person.introduce());
```

```javascript
var person = {
  name: "egoing",
  introduce: function() {
    return "My name is" + this.name;
  }
};
```

# 2. 생성자와 new

생성자는 객체를 만드는 역할을 하는 함수.

```javascript
function Person() {}
var p0 = Person(); //undefined
var p = new Person(); //Person{} 비어있는 객체
```

new Person()  
Person() 함수가 객체의 참조자가 될 수 있다.

자바는 클래스 안에 생성자가 있고, 클래스의 객체를 만든다.  
자바스크립트에서는 생성자가 함수일 뿐, 어디에 속해있지 않다.

```javascript
function Person(name) {
  this.name = name;
  this.introduce = function() {
    return "My name is " + this.name;
  };
}

var p1 = new Person("egoing");
var p2 = new Person("leezche");

document.write(p1.introduce()); //My name is egoing
document.write(p2.introduce()); //My name is leezche
```

생성자 Person이라는 함수를 정의했다.  
Person 앞에 new가 존재하기 때문에 함수가 아닌 생성자가 된다.  
생성자 함수가 하는 일은 객체의 초기화이며, 객체가 가지고 있는 정보, 객체가 할 수 있는 일을 정의하게 된다.  
덕분에 코드의 재사용성이 높아진다.

# 3. 함수와 this

전역변수와 전역함수는 모두 window라는 전역객체의 속성과 메소드다.  
<span class="clr-grey">node.js에서 전역객체는 global이다.</span>

## 1) 메소드와 this

this는 함수를 어떻게 호출하느냐에 따라 this를 가리키는 대상이 달라진다.

전역함수의 this는 window와 같다.

```javascript
function func() {
  if (window === this) {
    console.log("window === this");
  }
}
func(); //window === this
```

객체의 소속인 메소드의 this는 그 객체를 가르킨다.

```javascript
var o = {
  func: function() {
    if (o === this) {
      document.write("o === this");
    }
  }
};
o.func(); //o===this
```

## 2) 생성자와 this

```javascript
var funcThis = null;
function Func() {
  funcThis = this;
}

var o1 = Func();
if (funcThis === window) {
  document.write("window");
}

var o2 = new Func();
if (funcThis === o2) {
  document.write("o2 </br>");
}
// window, o2 둘다 출력됨.
```

Func를 생성자로 사용되면 this는 객체를 가리키고, 함수로 사용되면 this는 window는 가리킨다.

## 3) 객체로서 함수

```javascript
function sum(x, y) {
  return x + y;
}
var sum2 = new Function("x", "y", "return x+y");
```

sum이라는 함수는 sum이라는 객체를 만든 것이다.  
<span class="clr-grey">
function sum(){} 함수 리터럴  
var o ={} 객체리터럴  
var a = [0,1,2] 배열 리터럴  
편리하게 값을 만들 수 있는 문법적인 체계를 리터럴이라고 한다.
</span>

## 4) apply와 this

```javascript
var o = {};
var p = {};

function func() {
  switch (this) {
    case o:
      document.write("o");
      break;
    case p:
      document.write("p");
      break;
    case window:
      document.write("window");
      break;
  }
}

func(); //window
func.apply(o); //o
func.apply(p); //p
```

자바스크립트의 this는 변화무쌍하다.
