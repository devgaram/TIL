---
layout: post-content
title: (생활코딩) Javascript 입문1 
date: 2018-01-09
tags:
 - javascript
category: [javascript]
---

생활코딩 URL : https://opentutorials.org/course/743    
생활코딩 강의에서 기억하고 싶었던 내용을 포스팅해보았다.

---

# 변수의 효용을 이해하자.
- 필요에 의해서 변할 수 있는 영역과 변하지 않는 영역을 구분해서 코딩해야한다.
- 재활용 가능해진다. 유지보수 용이해진다.

# 비교
```javascript
alert( 1 === "1");   //false    데이터타입[형식]도 동일해야함.**이걸쓰자!
alert( 1 == "1");     //true     버그발생 가능성 높음..

var a;
alert(a);    //undefined 값이 정의되지 않음
alert(undefined == null)      //true
alert(undefined === null)    //false
alert( true == 1 )    //true **숫자 1은 true로 간주, 그 외는 false로.
alert( true === 1 )    //false 
alert(NaN === NaN);    //false
```

## [데이터타입]
undefined : 값이 정의되지 않은 상태, 프로그래머가 의도하지 않음 false로 간주    
null    :    값이 없는 상태, 프로그래머가 의도함. false로 간주    
NaN   : 0/0 계산할 수 없음. false로 간주

# 조건문
if('') alert('빈문자열'); //false 빈문자열은 false/ 문자열있으면 true로 간주