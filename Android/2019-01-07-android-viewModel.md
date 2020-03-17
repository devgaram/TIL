---
layout: post-content
title: 안드로이드 ViewModel에 대해 알아보자
date: 2019-01-07
tags: [android, viewmodel, mvvm]
category: [android]
---

안드로이드 아키텍쳐 중 ViewModel 클래스에 대한 내용입니다.

---

안드로이드 프레임워크는 액티비티와 프래그먼트와 같은 UI 컨트롤러의 라이프 사이클을 관리한다.

![액티비티가 회전을 거쳐 끝날 때까지의 라이프 사이클](/assets/images/viewmodel-lifecycle.png)    
[ UI 컨트롤러 라이프 사이클과 ViewModel 스코프]

아래와 같은 구성 변경 발생 시, 안드로이드는 UI 컨트롤러를 종료하거나 재생성한다.
* 런타임에 화면 방향이 전환되는 경우
* 언어, 글꼴 배율과 같은 기기 구성이 변경되는 경우
   
이 때 두가지 문제가 발생한다.

## 1) UI에 종속된 데이터 손실  
해결1 - 적은 데이터의 경우 <code class="codetainer">OnCreate()</code>에서 <code class="codetainer">onSaveInstanceState()</code> 메서드를 사용하여 번들에서 해당 데이터를 복원할 수 있다. 
<span class="clr-grey">많은 데이터와 비트맵에는 부적절하다.</span>    
해결2 - UI가 없는 워커 프래그먼트에 UI에 필요한 데이터를 관리하고 프래그먼트를 <code class="codetainer">SetRetainInstance(true)</code>로 설정함으로써 프래그먼트를 메모리에 유지(유보)시킨다.

## 2) 메모리 누수, 리소스 낭비    
UI 컨트롤러 재생성 시 다시 데이터를 로드하므로 리소스가 낭비되며, UI 컨트롤러가 비동기 호출을 하고 콜백을 받을 때 해당 컨트롤러가 파괴되었다면 에러가 발생하거나 메모리 누수가 날 수도 있다.

## 문제해결방법

UI 컨트롤러는 아래와 같은 작업을 다루는 경향이 있다.
* UI 데이터 표시
* 사용자 행동에 반응
* 권한 요청

그러므로 데이터베이스 또는 네트워크에서 데이터를 요청하고 로드하는 작업은 다른 클래스에 위임하여 UI 컨트롤러 로직에서 뷰 데이터에 대한 소유권을 분리하는 것이 효율적이다. 


# ViewModel 클래스 구현

[예제로 바로가기](/android/android-mvvm/#viewModel).

Architecture Components는 ViewModel 클래스를 제공한다.   
**ViewModel 클래스의 인스턴스(객체)는 구성 변경에도 데이터를 유지하며, ViewModel 인스턴스는 재생성된 액티비티에서 즉시 사용될 수 있다.**
소유자가 액티비티를 끝낼 때, 안드로이드는 ViewModel 객체의 <code class="codetainer">onCleared()</code> 메서드를 호출하여 리소스를 정리한다.

<span class="clr-note">
ViewModel 사용 시 ViewModel에 액티비티, 프래그먼트, 뷰에 대한 컨텍스트를 저장해서는 안된다.     
</span> 

<span class="clr-grey">액티비티가 재생성 될 때, ViewModel은 액티비티 생명주기 외부에 존재하기 때문에 UI 컨텍스트를 ViewModel에 저장하면 메모리 락을 발생시키는 직접적인 원인이 된다. 단, Application 컨텍스트(전체 앱의 수명주기)를 저장하는 것은 괜찮다. Application 컨텐스트는 AndroidViewModel 클래스를 통해 받을 수 있다.
