---
layout: post-content
title: 안드로이드 LiveData 대해 알아보자
date: 2019-01-09
tags: [android, livedata, mvvm]
category: [android]
---

LiveData 클래스를 통해 데이터 변경을 감지해보자.

---

[안드로이드 개발문서-LiveData](https://developer.android.com/topic/libraries/architecture/livedata#java).    

# LiveData는 LifecycleOwner의 라이프사이클을 알고있다.
LiveData 객체는 <code class="codetainer">observe()</code> 메서드를 통해 <code class="codetainer">LifecycleOwner</code>와 <code class="codetainer">Observer</code>객체를 페어로 등록한다.    
Observer 객체는 페어인 LifecycleOwner가 활성상태(<code class="codetainer">STARTED</code> 또는 <code class="codetainer">RESUMED</code>)일 때, 데이터 변화을 관측할 수 있다. 
반대로, LiveData 객체는 LifecycleOwner가 비활성 상태면 LiveData의 변화를 Observer에게 전달하지 않으며, LifecycleOwner가 <code class="codetainer">DESTROYED</code> 상태라면 자동으로 제거된다. 
이러한 LiveData의 라이프사이클의 인지는 메모리 누수에 대한 걱정을 덜어주며, UI 컴포넌트(액티비티, 프래그먼트)를 사용할 때 유용하다.    
<span class="clr-grey">**Note:** LifecycleOwner의 비활성 상태 예시) 액티비티가 백 스택에 올라가있을 때 </span>

# LiveData 사용시 이점
<span class="li-icon">일반적인 Observable과 달리, LifecycleOwner가 활성 상태일 때, LiveData의 변화를 Observer 객체에 전달함</span>
<span class="li-icon">충돌방지 : 액티비티가 백스택(비활성)에 있을 때, LiveData의 변화 이벤트를 Observer에게 전달하지 않음</span>
<span class="li-icon">메모리누수방지 : <code class="codetainer">DESTROYED</code> 시, LiveData 제거</span>
<span class="li-icon">생명주기 수동으로 관리할 필요 없음</span>
<span class="li-icon">항상 최신 데이터 유지 가능 : 액티비티가 다시 활성상태로 돌아온 즉시 최신 데이터를 받는다.</span>
<span class="li-icon">환경변화(예,화면회전)로 UI 컴포넌트 재생성 시, 즉시 최신 데이터를 받는다.</span>
<span class="li-icon">LiveData를 상속받아 앱에서 자원 공유 가능</span>

# LiveData 사용하기
[예제로 바로가기](/android/android-mvvm/#LiveData).
## 1. LiveData 객체 생성하기
LiveData는 보통 <code class="codetainer">Collections</code> 인터페이스를 구현한 클래스(List, Map, Set)를 사용하며, <code class="codetainer">ViewMoodel</code> 객체 안에 저장된다. 
```java
public class NameViewModel extends ViewModel {
  // String 타입의 LiveData 생성
  private MutableLiveData<String> mCurrentName;

    public MutableLiveData<String> getCurrentName() {
        if (mCurrentName == null) {
            mCurrentName = new MutableLiveData<String>();
        }
        return mCurrentName;
    }

// ..생략
}
```
## 2. LiveData 객체 관찰하기
<code class="codetainer">onCreate()</code> 메서드에서 LiveData 관찰을 시작하는 것이 좋다.
<span class="li-icon"><code class="codetainer">onResume()</code> 메서드에서 사용 시 중복 호출이 발생된다.</span>
<span class="li-icon"><code class="codetainer">STARTED</code> 상태가 되자마자 LiveData 객체의 최신 데이터를 받을 수 있다.</span>


```java
public class NameActivity extends AppCompatActivity {

    private NameViewModel mModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // ..생략

        // ViewModel 얻기
        mModel = ViewModelProviders.of(this).get(NameViewModel.class);


        // 옵저버 생성
        final Observer<String> nameObserver = new Observer<String>() {
            @Override
            public void onChanged(@Nullable final String newName) {
                // Update the UI, in this case, a TextView.
                mNameTextView.setText(newName);
            }
        };

        // LiveData 관찰 시작
        mModel.getCurrentName().observe(this, nameObserver);
    }
}
```
## 3. LiveData 객체 업데이트하기
LiveData는 데이터를 업데이트 하는 publid 메서드를 가지고 있지 않으므로, LiveData를 상속받은 <code class="codetainer">MutableLiveData</code>를 사용해야한다. 
<code class="codetainer">MutableLiveData</code>는 <code class="codetainer">public setValue(T)</code>와 <code class="codetainer">public postValue(T)</code> 메서드를 가지고 있어서 이를 통해 데이터를 변경할 수 있다.
```java
mButton.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        String anotherName = "John Doe";
        mModel.getCurrentName().setValue(anotherName);
    }
});
```
<code class="codetainer">setValue(T)</code>와 <code class="codetainer">postValue(T)</code>는 호출 시 Observer의 <code class="codetainer">onChanged()</code> 메서드가 호출되어 UI가 업데이트 된다.






