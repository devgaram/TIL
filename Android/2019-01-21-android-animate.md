---
layout: post-content
title: 안드로이드에서 이미지 애니메이션 구현 방법
date: 2019-01-21
tags: [android, animation]
category: [android]
---

이미지 애니메이션 구현 방법에는 다음과 같이 1)Animation Drawable과 2)Animated vector Drawable 두가지 옵션이 있다. 

---

[안드로이드 개발문서-Animate drwable graphics 바로가기](https://developer.android.com/guide/topics/graphics/drawable-animation?hl=ko)
[Icon Animation 참고 문서](https://www.androiddesignpatterns.com/2016/11/introduction-to-icon-animation-techniques.html)


**Animation Drawable** : 몇 개의 정적 drawable 파일들을 순차적으로 보여주는 방식으로 애니메이션을 구현하고 싶을 때(프레임 애니메이션)     
**AnimatedVectorDrawable** : 하나의 아이콘이 다른 이미지로 서서히 변화되는(morph) 애니메이션을 구현하고 싶을 때

# AnimationDrawable 사용하기
프레임 애니메이션 구현 [예제](https://github.com/devgaram/AndroidBudgeter/blob/master/app/src/main/res/drawable/ani_emotion.xml)

res/drawable/ani_emotion.xml 
```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/emotion_mouth_1" android:duration="5000" />
    <item android:drawable="@drawable/emotion_mouth_2" android:duration="5000" />
    <item android:drawable="@drawable/emotion_mouth_3" android:duration="5000" />
    <item android:drawable="@drawable/emotion_mouth_4" android:duration="5000" />
    <item android:drawable="@drawable/emotion_mouth_5" android:duration="5000" />
</animation-list>
```
<span class="li-icon"><code class="codetainer">android:oneshot</code> : true 한번만, false 반복</span>

res/drawable/emotion_mouth_1.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:top="@dimen/mb_l">
        <shape
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:shape="rectangle">
            <size
                android:width="120dp"
                android:height="60dp"></size>
            <solid android:color="@color/colorDefaultFont"/>
            <corners
                android:topRightRadius="60dp"
                android:topLeftRadius="60dp"></corners>
        </shape>
    </item>
</layer-list>
```
<span class="li-icon">ShapeDrawable로 입모양 그린 XML 파일</span>

res/drawable/activity_main.xml
```xml
<ImageView
            android:id="@+id/emotion_mouth"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:src="@drawable/ani_emotion" />
```

MainActivity.java
```java
AnimationDrawable emotionAnimation;

public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.main);

  ImageView emotionImage  = (ImageView) findViewById(R.id.emotion_mouth);
  emotionImage.setImageResource(R.drawable.ani_emotion);
  emotionAnimation = (AnimationDrawable) emotionImage.getDrawable();

  emotionImage.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
        emotionAnimation.start();
      }
  });
}
```
<span class="clr-note">**Note:** </span><span class="clr-grey"><code class="codetainer">onCreate()</code> 메서드에선 <code class="codetainer">AnimationDrawable</code>가 완전히 로드되지 않으므로 <code class="codetainer">start()</code> 메서드를 사용할 수 없고, 
만약 애니메이션 즉시 실행을 원한다면 액티비티의 <code class="codetainer">onStart()</code> 메서드에서 <code class="codetainer">start()</code> 메서드를 호출해야한다.</span>

# AnimatedVectorDrawable 사용하기
[안드로이드 개발문서-AnimatedVectorDrawable 바로가기](https://developer.android.com/reference/android/graphics/drawable/AnimatedVectorDrawable?hl=ko)

vectorDrawable은 흐려지거나 픽셀화되는 현상 없이 확장가능한 드로어블 타입이다.     
vectorDrawable 애니메이션을 구현하기 위해서는 3개의 xml 파일이 필요하다.     

## 1) vectorDrawable

|Element Name | Animatable attribute name|
|-------------------|--------------------------|
|`<vector>` | alpha|
|`<group>` | rotation, pivotX, pivotY, scaleX, scaleY, translateX, translateY|
|`<path>` | pathData, fillColor, strokeColor, strokeColor, strokeWidth, strokeAlpha, fillAlpha, trimPathStart, trimPathEnd, trimPathOffset|
|`<clip-path>` | pathData


```xml
 <!--res/drawable/vectordrawable.xml-->
 <vector xmlns:android="http://schemas.android.com/apk/res/android"
     android:height="64dp"
     android:width="64dp"
     android:viewportHeight="600"
     android:viewportWidth="600" >
     <group
         android:name="rotationGroup"
         android:pivotX="300.0"
         android:pivotY="300.0"
         android:rotation="45.0" >
         <path
             android:name="v"
             android:fillColor="#000000"
             android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
     </group>
 </vector>
```

## 2) AnimatedVectorDrawable
<span class="li-nonicon">한 개의 `android:drawable` 속성과 하나 이상의 `<target>`을 가진다.</span>
<span class="li-nonicon">`<target>` 은 `android:name` 속성으로 `ObjectAnimator` 또는 `AnimatorSet`의 타겟이 되는 `vectorDrawable`을 명시한다.
<span class="clr-grey">**Note:** 타겟은 vectorDrawable의 group element 또는 path element가 될 수 있다.</span></span>


```xml
<!--res/drawable/animatedVector.xml-->
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
     android:drawable="@drawable/vectordrawable" >
     <target
         android:name="rotationGroup"
         android:animation="@animator/rotation" />
     <target
         android:name="v"
         android:animation="@animator/path_morph" />
 </animated-vector>
```
<span class="li-icon">vectorDrawable에서 rotationGroup 이름 속성을 가진 `<group>` 엘리먼트에 rotation 애니메이션을 적용</span>
<span class="li-icon">vectorDrawable에서 v 이름 속성을 가진 `<path>` 엘리먼트에 path_morph 애니메이션을 적용</span>

## 3) ObjectAnimator or AnimatorSet 애니메이션 정의
애니메이션은 `ObjectAnimator` 와 `AnimatorSet`으로 정의할 수 있다.

```xml
<!-- res/animator/rotation.xml -->
<objectAnimator
    android:duration="6000"
    android:propertyName="rotation"
    android:valueFrom="0"
    android:valueTo="360" />
```
6초동안 360도 회전하는 애니메이션

```xml
<!-- res/animator/path_morph.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <objectAnimator
        android:duration="3000"
        android:propertyName="pathData"
        android:valueFrom="M300,70 l 0,-70 70,70 0,0   -70,70z"
        android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
        android:valueType="pathType" />
</set>
```
3초동안 모양 바뀌는 애니메이션(morph)     
<span class="clr-note">**Note:** `android:valueFrom` 과 `android:valueTo`의 명령 순서와 파라미터 값은 동일해야한다.</span>     
<span class="clr-grey">**Note:**  path 값은 string 리소스에 저장하는 것을 추천한다.</span>

## 4) 3개의 xml을 AAPT 툴의 지원으로 하나의 xml에 정의할 수 있다.

```xml
 <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:aapt="http://schemas.android.com/aapt" >
     <aapt:attr name="android:drawable">
         <vector
             android:height="64dp"
             android:width="64dp"
             android:viewportHeight="600"
             android:viewportWidth="600" >
             <group
                 android:name="rotationGroup"
                 android:pivotX="300.0"
                 android:pivotY="300.0"
                 android:rotation="45.0" >
                 <path
                     android:name="v"
                     android:fillColor="#000000"
                     android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
             </group>
         </vector>
     </aapt:attr>

     <target android:name="rotationGroup"> *
         <aapt:attr name="android:animation">
             <objectAnimator
             android:duration="6000"
             android:propertyName="rotation"
             android:valueFrom="0"
             android:valueTo="360" />
         </aapt:attr>
     </target>

     <target android:name="v" >
         <aapt:attr name="android:animation">
             <set>
                 <objectAnimator
                     android:duration="3000"
                     android:propertyName="pathData"
                     android:valueFrom="M300,70 l 0,-70 70,70 0,0 -70,70z"
                     android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
                     android:valueType="pathType"/>
             </set>
         </aapt:attr>
      </target>
 </animated-vector>
```

## 5) 정의한 애니메이션 적용하기

res/drawable/activity_main.xml
```xml
<ImageView
            android:id="@+id/emotion_mouth"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:src="@drawable/animatedVector" />
```

MainActivity.java
```java
 ImageView emotionImage;

public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.main);

  emotionImage  = (ImageView) findViewById(R.id.emotion_mouth);
  
  emotionImage.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
        Drawable drawable = emotionImage.getDrawable();
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                if (drawable instanceof AnimatedVectorDrawable) {
                    AnimatedVectorDrawable animatedVectorDrawable = (AnimatedVectorDrawable) drawable;
                    animatedVectorDrawable.start();
                }
            } else {
                if (drawable instanceof AnimatedVectorDrawableCompat) {
                    AnimatedVectorDrawableCompat animatedVectorDrawableCompat = (AnimatedVectorDrawableCompat) drawable;
                    animatedVectorDrawableCompat.start();
                }
            }
      }
  });
  
}
```