---
layout: post-content
title: (생활코딩) javascript 객체지향2
date: 2018-01-13
tags:
 - javascript
category: [javascript]
---

생활코딩 객체지향 파트 중 상속과 프로토타입에 대한 내용을 정리했습니다.

---

# 1. 상속

상속을 하기 위한 준비 작업
```javascript
function Person(name){ 
    this.name = name; 
} 
 
Person.prototype.name = null; 
Person.prototype.introduce = function(){ 
    return 'My name is' + this.name; 
} 
 
var p1 = new Person('egoing'); 
document.write(p1.introduce());    //My name is egoing
```
객체의 프로퍼티 정의 방법은 생성자를 이용하는 것과 프로토타입을 이용하는 것이 있다.    
Person 객체에는 prototype이라는 속성이 있는 데 그 안에는 어떤 객체가 있다.    
.name을 통해 그 객체에 값을 줄 수 있다.


```javascript
function Person(name){ 
   this.name = name; 
}
Person.prototype.name = null; 
Person.prototype.introduce = function(){ 
    return 'My name is' + this.name; 
}
function Programmer(name){ 
    this.name = name; 
}
 function Designer(name){ 
    this.name = name; 
}
 
/**상속방법**/
Designer.prototype = new Person();
Designer.prototype.design= function(){
    return "beautiful!"; 
} 
Programmer.prototype = new Person(); 
Programmer.prototype.coding = function(){ 
    return "hello world!"; 
}
 
var p1 = new Programmer('egoing'); 
document.write(p1.introduce() + '');    //My name is egoing이 출력됨 
document.write(p1.coding() + '');    //hello world! 
 
var p2 = new Desiner('leezche'); 
document.write(p2.design() + '');    //beautiful!
```
Programmer에는 introduce 속성이 정의되어 있지않은데 어떻게 사용할 수 있었을까?    
Person의 introduce를 상속받았기 때문이다.

객체를 생성할 때, 자바스크립트는 생성자 함수가 prototype라는 프로퍼티를 갖고 있는 지 확인한다.    
그리고 생성자 함수에 들어있는 객체와 똑같은 객체를 만들어서 생성자의 결과로 리턴을 해준다.

name과 introduce라는 프로퍼티가 prototype이라는 객체가 가지고 있다.   
즉, prototype이라는 객체를 리턴한다?    
그래서 Programmer의 prototype이라는 프로퍼티에 저장된다.

<span class="clr-note">
*생성자의 prototype에 상속받을 객체를 new 한다.    
*Programmer.prototype = new Person()
</span>

기본적으로 가지고있는 property를 가진 객체를 생성하기 위해서 객체 리터럴 대신, new라는 키워드를 사용한다.


# 2. prototype
```javascript
function Ultra(){} 
Ultra.prototype.ultraProp = true; 

function Super(){} 
Super.prototype = new Ultra(); 
 
function sub(){} 
Sub.prototype = new Super(); 
 
var o = new Sub() 
console.log(o.ultraProp);    //true출력됨. 
 
//1) 
o.ultraProp = 1; 
console.log(o.ultraProp);    //1
```
o.ultraProp o라는 객체가 ultraProp를 가지고 있는지 찾는다. 그래서 1이 출력됨.


```javascript
function Ultra(){} 
Ultra.prototype.ultraProp = true; 
 
function Super(){} 
Super.prototype = new Ultra(); 
 
function sub(){} 
Sub.prototype = new Super(); 
Sub.prototype.ultraProp = 2; 
 
var o = new Sub() 
console.log(o.ultraProp);    //2가 출력됨
```
o객체에 ultraProp 프로퍼티를 찾는다. 없으면 o라는 객체의 생성자를 알아 낸후, 이 생성자의 프로토타입 객체를 뒤져서 그 객체의 propery에 ultraProp가 있는 지 찾는다.


```javascript
function Ultra(){} 
Ultra.prototype.ultraProp = true; 
 
function Super(){} 
Super.prototype = new Ultra(); 
 
function sub(){} 
var s = new Super(); 
s.ultraProp = 3; 
Sub.prototype = s;  
 
var o = new Sub() 
console.log(o.ultraProp);    //3이 출력됨
```