---
layout: post-content
title: (안드로이드) RecyclerView를 사용하자
date: 2018-12-05
tags:
 - android
category: [android]
---

[될 때까지 안드로이드]의 RecyclerView 파트를 정리한 내용입니다.

어댑터 뷰의 성능을 개선한 컴포넌트로 애니메이션을 지원하고 뷰홀더 패턴을 강제한다.    
대신 이벤트 리스너와 커서를 지원하지 않는 단점이 있다.

---

## 어댑터 뷰의 단점
1. 빠르게 스크롤 할 때 성능문제와 불필요한 지연 문제가 있다.
2. 데이터 목록이 변경되었을 때, <code class="codetainer">notifyDataSetChanged()</code> 메서드를 빈번하게 호출하여 전체 아이템을 갱신하는 데 비용이 많이 든다. 이 메서드는 항상 전체 항목을 새로 로드한다.

## 리사이클러 뷰 vs 어댑터 뷰
* 상속 받는 클래스 : RecyclerView.Adapter vs BaseAdapter
* 리사이클러 뷰는 레이아웃 매니저를 지정해줘야 한다.
* 리사이클러 뷰는 뷰홀더 패턴을 반드시 구현해야한다. 

## 리사이클러 뷰 관련 클래스
1. RecyclerView.Adapter : 어댑터 역할
2. RecyclerView.ViewHolder : 뷰홀더 클래스가 상속받아야 할 클래스
3. LayoutManager : 아이템을 어떻게 배치할 것 인가 
	* LinearLayoutManager
	* GridLayoutManager
	* StaggeredGridLayoutManager 

4. RecyclerView.ItemAnimator : 아이템이 추가, 삭제, 재정렬 시 애니메이션 어떻게 할 것인가
5. RecyclerView.ItemDecoration : 아이템을 세부적으로 어떻게 꾸밀 것인가

## 리사이클러 통지 메서드
* <code class="codetainer">notifyItemInserted(int position)</code> : position 위치의 아이템이 삽입된 것을 통지
* <code class="codetainer">notifyItemRemoved(int position)</code> : position 위치의 아이템이 삭제된 것을 통지

## 관련 메서드
* <code class="codetainer">void setHasFixedSize (boolean hasFixedSize)</code> : 각 아이템의 변화가 리사이클러 뷰의 전체 크기에 영향을 끼치지 않는다면 true를 사용한다.


