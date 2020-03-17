---
layout: post-content
title: (생활코딩) Javascript 입문3
date: 2018-01-11
tags:
 - javascript
category: [javascript]
---

생활코딩 javscript 과정 중 정규표현식 내용을 정리했습니다.

---


# 정규표현식(Regular Expression)
- 문자열에서 특정 문자를 찾을 때 사용 .. * 나중에 생활코딩 정규표현식 강의도 듣기!! & 전화번호 때 사용했던 경험 넣기
- 컴파일 -> 실행 단계로 이뤄진다.
- 컴파일 : 찾고 싶은 패턴을 만듬.
- 실행 : 문자열에서 패턴을 추출.

## 정규표현식 객체를 만드는 방법
1) var pattern = /a/    
2) var pattern = new RegExp('a');

## 패턴 추출
1) pattern.exec('abcdef')    // ["a"]; a배열 추출    
2) pattern.exec('bcdefg')    // null a가 없어서.    
3) pattern.test('abcdef')     // true    
4) pattern.test('bcdefg')     // false

## 문자열 객체의 메소드에서 정규표현식 사용하기
String.match()    
String.replace()
```javascript
'abcdef'.match(pattern)    // ["a"]
'bcdefg'.match(pattern)    // null
'abcdef'.replace(pattern,'A')    // Abcdef
```

## 옵션
i 대소문자 구분 안함
```javascript
var oi = /a/i;
"Abcdef".match(oi);    //["A"]
```

g 검색된 모든 결과 리턴
```
var og = /a/g;
"abcdea".match(og);    //["a","a"]
```

## 치환
```
var pattern = /(\w+)\s(\w+)/;    //()는 그룹, \s는 공백, +는 1개이상, \w는 문자(0-9,a-z,A-Z)
var str = "coding everybody";
var result = str.replace(pattern, "$2, $1");    //$N는 N번째 그룹.
console.log(result);
```