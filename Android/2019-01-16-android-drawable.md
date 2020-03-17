---
layout: post-content
title: (Drawable 리소스) LayerDrawable과 ShapeDrawable을 이용해 둥근모서리와 그림자 있는 배경 만들기.
date: 2019-01-16
tags: [android, resource]
category: [android]
---

LayerDrawable과 ShapeDrawable을 이용해 둥근모서리+그림자+투명배경이 있는 디자인을 다른 XML 리소스의 background로 적용하는 방법입니다.

---

[안드로이드 개발문서-Drawable 바로가기](https://developer.android.com/guide/topics/resources/drawable-resource?hl=ko)

## 드로어블(Drawable) 리소스
앱 화면에 그리고 싶은 것을 XML 파일로 미리 정의할 수 있으며, <code class="codetainer">getDrawable(int)</code>와 같은 API를 사용하여 가져오거나 
<code class="codetainer">android:drawable</code> 및 <code class="codetainer">android:icon</code>과 같은 속성을 사용하여 다른 XML 리소스에 적용할 수 있다.     
<span class="clr-grey">**Example)** android:background="@drawable/custom_layer_resource"</span>

# LayerDrawable
각 `<item>` 요소는 순서대로 그려지므로, 맨 마지막 `<item>`이 맨 위에 보이게 된다.    

<span class="li-nonicon">위치 : res/drawable/filename.xml</span>
<span class="li-nonicon">Java 리소스 참조 : R.drawable.filename</span>
<span class="li-nonicon">XML 리소스 참조 : @[package:]drawable/filename</span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list
    xmlns:android="http://schemas.android.com/apk/res/android" ><!-- XML 네임스페이스 정의 필수 -->
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:id="@[+][package:]id/resource_name"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension" /> 
</layer-list>
```
<span class="li-icon"><code class="codetainer">android:top="dimension"</code> top에서 dimension 값만큼 padding</span>

# ShapeDrawable

<span class="li-nonicon">위치 : res/drawable/filename.xml</span>
<span class="li-nonicon">Java 리소스 참조 : R.drawable.filename</span>
<span class="li-nonicon">XML 리소스 참조 : @[package:]drawable/filename</span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    <corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient
        android:angle="integer"
        android:centerX="float"
        android:centerY="float"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size
        android:width="integer"
        android:height="integer" />
    <solid
        android:color="color" />
    <stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>
```
<span class="li-icon"><code class="codetainer">corners : </code>둥근모서리</span>
<span class="li-icon"><code class="codetainer">gradient : </code>그라데이션 색상</span>
<span class="li-icon"><code class="codetainer">padding : </code>shape가 아닌 view 요소에 적용할 패딩</span>
<span class="li-icon"><code class="codetainer">size : </code>shape 크기</span>
<span class="li-icon"><code class="codetainer">solid : </code>채우기 색상</span>
<span class="li-icon"><code class="codetainer">stroke : </code>선 속성</span>

# 실전예제

1) 둥근모서리+그림자+투명배경 드로어블 생성하기     

res/drawable/round_border.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item>
        <shape android:shape="rectangle" >            
            <solid android:color="@android:color/darker_gray" />
        </shape>
    </item>
    <item
        android:bottom="-2dp"
        android:left="2dp"
        android:right="2dp"
        android:top="2dp">
        <shape android:shape="rectangle" >
            <stroke
                android:width="0.75dp"
                android:color="#f5f5f5" />
            <corners
                android:topLeftRadius="10dp"
                android:topRightRadius="10dp"/>
            <solid android:color="#3cffffff" />
        </shape>
    </item>
</layer-list>
```
2) round_border를 LinearLayout의 배경으로 적용하기
```xml
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="10dp"
        android:background="@drawable/round_border">
</LinearLayout>
```