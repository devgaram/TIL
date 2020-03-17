---
layout: post-content
title: (안드로이드) 어댑터뷰를 사용해보자
date: 2018-12-02
tags:
 - android
category: [android]
---

[될때까지 안드로이드] 어댑터뷰 파트의 내용을 정리한 글입니다.

ListView 와 GridView 
- 반복되고 양이 많은 데이터를 표시하고 싶을 때
- 이미지와 같이 메모리를 차지하는 리소스를 표시하고 싶을 때    
- 모두 AdapterView 추상클래스를 상속받아 어댑터 패턴을 사용하여 데이터를 뷰에 표시한다.
- ScrollView의 경우 한번에 모든 컨텐츠를 로드하므로 컨텐츠가 메모리를 많이 사용할 경우 메모리 부족으로 앱이 종료될 수 있다.

# AdapterView
---
AdapterView를 상속받은 뷰들은 화면에 보이는 내용만 로드하는 기법을 사용한다.

## 1. AdapterView의 구현

데이터 준비하기
```java
//MainActivity.java
ArrayList<Weather> data = new ArrayList<Weather>()
data.add(new Weather("수원", "25도", "맑음"));
data.add(new Weather("안양", "22도", "비"));
data.add(new Weather("부천", "22도", "구름"));
data.add(new Weather("성남", "24도", "맑음"));
data.add(new Weather("서울", "28도", "구름"));
data.add(new Weather("광주", "30도", "비"));
data.add(new Weather("부산", "20도", "비"));
```

데이터를 뷰에 연결해 줄 어댑터를 준비한다.

```java
//MainActivity.java
MyFirstAdapter adapter = new MyFirstAdapter(data);
```

뷰에 어댑터를 붙인다.
```java
//MainActivity.java
ListView listView = (ListView) findViewById(R.id.list_view);
listView.setAdapter(adapter);
```

## 2. 클릭 이벤트 구현    
public static interface AdapterView.OnItemClickListener      
<span class="clr-grey">android.widget.AdapterView.OnItemClickListener : 어댑터뷰의 아이템 클릭시 호출되는 콜백 메서드를 정의한 인터페이스</span>

public abstract void onItemClick (AdapterView<?> parent, View view, int position, long id)     
<span class="clr-grey">
parent : 클릭이 일어난 AdapterView    
view : AdapterView 안의 클릭된 View = 클릭된 아이템 뷰    
position : 클릭된 아이템 뷰의 위치    
id : 클릭된 아이템 뷰의 row ID
</span>
```java
listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
  @Override
  public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
    Toast.makeText(MainActivity.this, position + " 번째 아이템 선택", Toast.LENGTH_SHORT).show();
   }
});
```

## 3. 예제 구성

1. Weather.java
날씨를 표현하는 모델클래스    
<span class="clr-grey"> toString() : 모든 클래스가 가지고 있는 기본메서디로 디버깅이나 로그에서 정보 확인을 위해 toString() 메서드 재정의한다. </span>

2. MyFirstAdapter.java    
추상클래스인 BaseAdapter를 상속받는 클래스로 추상 메서드들을 구현해야 한다.    
<span class="clr-grey"> Note: BaseAdapter는 어댑터의 기능을 추상화해 둔 추상 클래스, 각 메서드 재정의 필요 </span>

```java
 @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        /**
         * LayoutInflater 클래스란?
         * Activity 이외의 클래스에서 Context를 통해 XML로 정의한 레이아웃을 로드하여 View로 반환해주는 클래스
         */
        ViewHolder holder;
        if (convertView == null) {
            holder = new ViewHolder();
            convertView = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_weather, parent, false);
            ImageView weatherImage = (ImageView) convertView.findViewById(R.id.weather_image);
            TextView cityText = (TextView) convertView.findViewById(R.id.city_text);
            TextView tempText = (TextView) convertView.findViewById(R.id.temp_text);
            holder.cityText = cityText;
            holder.tempText = tempText;
            holder.weatherImage = weatherImage;
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }

        Weather weather = mData.get(position);
        holder.cityText.setText(weather.getCity());
        holder.tempText.setText(weather.getTemp());
        holder.weatherImage.setImageResource(mWeatherImageMap.get(weather.getWeather()));
        return convertView;
    }
    static class ViewHolder {
        ImageView weatherImage;
        TextView cityText;
        TextView tempText;
    }
```
* 핵심메서드 getView() 작성    
    - 각 아이템이 화면에 표시될 때마다 호출되며, 여기서 화면에 표시할 레이아웃과 데이터를 모두 작성해야한다.    
    - ListView의 각 아이템에 해당되는 View는 화면에 보이는 만큼만 생성되고 스크롤 시에 안쓰이는 아이템은 새로 보이는 아이템의 View로 재사용된다.    
    <span class="clr-grey"> Note: 두번째 파라미터인 convertView가 재사용 시에 이전에 생성되었던 getView()가 반환했던 View </span>
* LayoutInflater 추상 클래스
    - Activity 이외의 클래스에서 Context를 통해 XML로 정의한 레이아웃을 로드하여 View로 반환해주는 클래스
    - 레이아웃 XML 파일을 View 객체로 반환한다.
    - LayoutInflater 인스턴스 받는 법 : **Activity.getLayoutInflater()** or **Context.getSystemService(Class)**
    - Public methods    
    <span class="clr-grey">
	    -LayoutInflater.from(parent.getContext())    
		현재 Context로부터 LayoutInflater 인스턴스를 반환받는다.*static LayoutInflater*    
        -LayoutInflater.from(parent.getContext()).inflate(R.layout.item_weather, parent, false);    
    	inflate(int resource, ViewGroup root, boolean attachToRoot)    
        xml파일 item_weather을 View 객체 형태로 반환한다.
    </span>
* ViewHolder
자주 사용하는 뷰를 한번 로드하면 재사용하고 표시할 내용만 교체하기 위한 패턴
    - View 안에 여러 데이터를 담고 싶을 때, getTag(), setTag()를 사용한다.    
    <span class="clr-grey">android.view.View, Tag는 Object 타입이라 다양한 용도로 사용가능</span>