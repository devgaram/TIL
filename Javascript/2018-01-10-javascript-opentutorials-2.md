---
layout: post-content
title: (생활코딩) Javascript 입문2
date: 2018-01-10
tags:
 - javascript
category: [javascript]
---

생활코딩 URL https://opentutorials.org/course/743/    
반복문, 함수 정의방법, 배열, 객체

---

# 반복문
```javascript
for( var i=0; i<10; i++){
    if( i === 5) break;     // 그 즉시, for문 중단 - coding1~4 까지만 출력됨
    if( i === 5) continue; // 실행을 중단하고 반복문 이어서 함. - coding1~4/6~9까지 출력됨.
    document.write("coding"+i+"");
}
```

# 함수 정의 방법
```javascript
// [첫번째 방법]
function numbering(arg){
   //statement
}

// [두번째 방법]
var numbering = function(){
 //statement 
 }

 // [세번째 방법-익명함수]
 (function(){
    //statement 
 })();
 //*일회성 함수
```

# 배열
```javascript
var member = ['a', 'b', 'c'];    //생성
alert(member[0]);                // 'a' 

for(var i=0; i<member.length; i++){    // 배열의 크기만큼 반복
  document.write(member[i].toUpperCase()) + "";
}
/*배열의 원소 추가하는 방법*/
member.push('f');        // a,b,c,f - 배열의 맨 뒤에 삽입
member.concat(['e','f']); // a,b,c,f,e,f - 복수의 원소 추가
member.unshift('g');     // g,a,b,c,f,e,f - 배열의 맨 앞에 삽입
member.splice(2, 0, 'B'); // g,a,B,b,c,f,e,f - 배열의 2번째 인덱스 0(앞에) B를 추가

/*배열의 원소 삭제하는 방법 및 정렬 */
member.shift();        // 배열의 첫번째 원소 삭제
member.pop();        // 배열의 마지막 원소 삭제
member.sort();        // 오름차순 정렬
member.reverse();    // 내림차순 정렬
```

## 배열의 함수. splice
- 배열의 특정구간을 추출하거나, 특정 구간에 특정 배열을 추가한다.
- array.splice( index, howmany, [element1...N])    
<span class="clr-grey">
index : 배열의 위치    
howmany : index에서부터 제거될 원소의 수로, 1이면 index 값만 제거되어 값이 추가된다.   
0일 때는 삭제 없이 인덱스 앞쪽에 값이 추가됨. 없으면 배열 끝까지(길이)    
element1...N : 추가될 Value
</span>

## 배열의 정렬. sort - 사용자정의가능
```javascript
function sortNumber(a,b){
    return a-b;    //리턴값이 음수, 양수, 0인지에 따라 순서 정함.
}
var numbers = [20 ,10 8, 15]
numbers.sort(sortNumber); // 8,10,15,20
```

# 객체
배열 - 자동으로 색인 추가[number], 순서존재    
객체 - 인덱스의 값을 숫자 뿐만 아니라 문자 등 원하는 이름으로 지정 가능 (데이터타입 : 연관배열, 맵, 딕셔너리), 순서존재X
```javascript
/*객체의 생성*/
var grades = {'aa':10, 'bb':6, 'cc': 80};    //객체의 생성
var grades = {};    //비어있는 객체의 생성
var grades = new object();    //비어있는 객체의 생성

/*객체의 할당*/
grades['aa'] = 10;
grades['bb'] = 6;
grades['cc'] = 80;

/*객체의 접근 key/value*/
grades['bb']    //6
grades.bb       //6
grades['b'+'b'] //6
grades.'b'+'b';  //에러 남

/*객체의 값 가져오기*/
for(var key in grades){    //객체를 구성하는 값들의 키값만 하나씩 가져옴
 document.write("key : "+key+" value : "+grades[key] + "");
}

/*객체지향 프로그래밍 - 서로 연관된 데이터, 처리를 하나의 그릇안에 그룹핑 한것*/
var grades = {
 'list' : {'aa':10, 'bb':6, 'cc': 80},
 'show' : function(){
      alert("hello world"); 
  },
'show2' : function(){
      console.log(this.list);     //this란? 함수가 속해있는 객체를 가리킨다. 여기선 grades를 가리킴.
 },
'show_list' : function(){
  for(var name in this.list){
   console.log(name,  this.list[name]);
  }
 }
}
alert(grades['list]['aa']); //10
grades['show']();       //객체의 함수호출. alert창 뜸.
grades.show_list();    //객체의 함수호출 aa,10 bb,6 cc,80 출력
```