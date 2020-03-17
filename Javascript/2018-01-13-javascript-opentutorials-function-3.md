---
layout: post-content
title: (생활코딩) javascript 함수지향3
date: 2018-01-13
tags:
 - javascript
category: [javascript]
---

생활코딩 함수지향 파트 중 클로저에 대한 내용을 정리했습니다.

---

# 1. 클로저의 개념
```javascript
function outter() { 
  var title = 'coding everybody'; 
  return function(){ 
    alert(title); 
  } 
} 
inner = outter(); 
inner();
```
함수 outter의 리턴 값은 내부함수이며 변수 inner에 내부함수가 값으로 저장된다.    
outter 호출이 종료된 순간, outter는 사라진다.    
하지만, inner()를 실행했을 때 알럿창에 coding everybody가 보인다.

왜?    
<span class="clr-note">내부함수는 외부함수가 종료되도 외부함수에 접근할 수 있다.</span>


# 2. private variable
```javascript
function factory_movie(title){ 
    return{ 
        get_title : function(){ 
            return title; 
        }, 
        set_tile : function(_title){ 
            if(typeof _title === 'String'){ 
                title = _title; 
            } else{ 
                alert('제목은 문자열이여야 합니다.'); 
            } 
        } 
    } 
} 
ghost = factory_movie('Ghost in the shell'); 
matrix = factory_movie('Matrix'); 
alert(ghost.get_title());    //Ghost in the shell 
alert(matrix.get_tiel());    //Matrix
```
factory_movie의 리턴 값은 get_title, set_title 메소드를 속성으로 가지고 있는 객체이다.    
ghost, matrix 변수는 객체를 담게된다.    
factory_movie 호출 후, 함수의 생이 종료되어도 ghost, matrix를 통해 함수의 지역변수인 title에 접근할 수 있다.

왜 private variable을 사용해야 하는가?    
<span class="clr-note">외부에서 title 변수를 수정해도 ghost, matrix가 가진 title의 맥락에는 영향을 주지 않는다.    즉, 데이터의 수정과 저장을 안전하게 할 수 있다.</span>


# 3. 클로저의 응용
```javascript
var arr = []; 
for( var i=0; i<5; i++){
     arr[i] = function(){ 
        console.log(i);
     } 
} 
 
for( var index in arr ){ 
    console.log(arr[index]()); 
} 
//결과는 5 5 5 5 5
```
함수가 실행되는 시점의 변수가 내부함수에 저장된다.    
두번째 반목문에서 배열객체의 함수를 실행하는 순간의 i가 콘솔에 찍히게 된다.    
그러나, i는 첫번째 포문에 의해 이미 5가 되어버린 상태이므로 5만 다섯번 찍히게 된다.


```javascript
var arr = []; 
for(var i=0; i<5; i++){ 
    arr[i] = function(id){ 
        return function(){ 
            return id; 
        } 
    }(i); 
} 
 
for( var index in arr){ 
    console.log(arr[index]());  
} 
//결과는 0,1,2,3,4
```
외부함수의 인자 값으로 i가 전달되고, 내부함수는 이 시점의 i를 저장한다.    
첫번째 포문에서 외부함수가 i를 인자값으로 하면서 실행되는 시점에서 내부함수가 정의된다.


