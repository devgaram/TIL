---
layout: post-content
title: (안드로이드) fragment를 사용하자
date: 2018-12-07
tags:
 - android
category: [android]
---

[될 때까지 안드로이드]의 fragment 파트를 정리한 내용입니다.

[기본 프래그먼트 구현 예제](https://github.com/devgaram/androidExample/tree/master/fragmentexam).    
[다이얼로그 프래그먼트 구현 예제](https://github.com/devgaram/androidExample/tree/master/exitdialogfragment).    
[콜백 구현 예제](https://github.com/devgaram/androidExample/tree/master/callbackexam).    

여러 개의 프래그먼트를 하나의 액티비티에 조합하여 창이 여러 개인 UI를 구축할 때 사용할 수 있다.
* 하나의 프래그먼트를 여러 액티비티에서 재사용할 수 있으며,동적으로 추가, 삭제, 교체가 쉽다.    
* 프래그먼트는 자체 수명 주기를 가진다.
* 프래그먼트는 부모-자식 관계를 가질 수 있다.

---

# 생명주기 (소속 액티비티가 실행 중일 때)

최소한 다음과 같은 수명 주기 메서드를 구현해야한다.

**onCreate()**    
프래그먼트를 생성할 때 호출되는 콜백 메서드   
프래그먼트가 일시정지되거나 중지되었다가 재개되었을 때 유지하고자 하는 것을 초기화하는 부분

**onCreateView()**    
액티비티는 <code class="codetainer">onCreate()</code> 콜백 메서드에서 <code class="codetainer">setContentView()</code> 메서드를 호출하여 View 객체(레이아웃)을 가져온다.    
프래그먼트는 <code class="codetainer">onCreateView()</code> 콜백 메서드에서 LayoutInflater를 통해 레이아웃을 가져온다. 

**onPause()**    
시스템이 이 메서드를 호출하는 것은 사용자가 프래그먼트를 떠난다는 첫 번째 신호.   
현재 사용자 세션을 넘어서 지속되어야 하는 변경 사항을 저장하는 부분

프래그먼트 추가   
onAttach() -> onCreate() -> onCreateView() -> onActivityCreated() -> onStart() -> onResume()

프래그먼트 소멸     
onPause() -> onStop() -> onDestroyView() -> onDestroy() -> onDetach()

# 실습

## 프래그먼트의 생성자
생성자를 오버로드할 수 없으며, 생성자를 통해 파라미터 전달을 금지하고 있다.    
재생성 시에 정보를 자동으로 저장 및 복원하기 위한 설계가 이미 되어 있고 그것을 따르기 위한 제약이다. 
프래그먼트의 생성과 동시에 파라미터를 전달하는 방법은 **Bundle 객체**를 활용한다.

```java
public class ColorFragment extends Fragment {

    public ColorFragment() {
        // Required empty public constructor
    }
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_color, container, false);
    }
}
```


## 프래그먼트 매니저
프래그먼트 조작을 위해 프래그먼트 매니저가 필요하다.    
프래그먼트 매니저는 액티비티 처럼 백스택을 가지고 있음.
* 액티비티 내 XML에 포함된 프래그먼트를 <code class="codetainer">findFragmentById()</code> 메서드로 가져오기 또는 <code class="codetainer">findFragmentByTag()</code>로 가져오기
* 액티비티 백스택에서 프래그먼트를 <code class="codetainer">popBackStack()</code>메서드로 빠져나오게 하여 액티비티의 뒤로 가기와 같은 효과를 냄 ( <code class="codetainer">addToBackStack()</code> : 프래그먼트 매니저의 백스택에 프래그먼트 추가하는 메서드 )

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      ...생략...
        FragmentManager fragmentManager = getSupportFragmentManager();
        ColorFragment colorFragment = (ColorFragment) fragmentManager.findFragmentById(R.id.color_fragment);
        colorFragment.setColor(Color.BLUE);
    }
}
```
## 프래그먼트에서 액티비티에 접근하는 방법

```java
View listView = getActivity().findViewById(R.id.list);
```

## 프래그먼트 교체, 삭제, 추가
프래그먼트를 교체하기 위해서는 <fragment<fragment>>를 <FrameLayout<FrameLayout>>과 같은 레이아웃으로 감싸줘야한다.    
**프래그먼트 트랜지션 수행** add(), remove(), replace() 같은 메서드를 사용하고 commit()을 수행하면 트랜지션이 적용된다.
    
```java
public class MainActivity extends AppCompatActivity {

  ...생략....
  
    public void change(View view) {
        ColorFragment fragment = new ColorFragment();
        int red = new Random().nextInt(256);
        int green = new Random().nextInt(256);
        int blue = new Random().nextInt(256);
        fragment.setColor(Color.rgb(red, green, blue));
        getSupportFragmentManager()
                .beginTransaction()
                .replace(R.id.container, fragment)
                .commit();
    }
}
```

## 프래그먼트와 액티비티 간의 통신

일반적으로 액티비티에서 프래그먼트나 다른 뷰들의 인스턴스를 가지고 있어서 이들의 메서드를 호출하는 식이다. 따라서 프래그먼트의 상태가 변했을 때 액티비티에서 이것을 알아차리려면 계속해서 프래그먼트의 상태 값을 알아내는 메서드를 호출해야한다.


그러나 콜백 인터페이스를 사용하여 프래그먼트의 상태가 변할 때마다 자동으로 액티비티에게 알려줄 수 있다. 
Button의 <code class="codetainer">onClick()</code> 이벤트나 액티비티의 <code class="codetainer">onCreate()</code>가 대표적인 콜백 메서드이며, 호출자 입장에서는 피호출자의 변화를 감시하다가 알아채기 때문에 리스너(Listener) 라고도 불른다.

