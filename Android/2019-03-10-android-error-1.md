---
layout: post-content
title: 안드로이드 에러 - setText의 파라미터로 숫자를 전달했을 때
date: 2019-03-10
tags: [android, error]
category: [android]
---

setText의 파라미터로 숫자를 전달했을 때 발생되는 에러로그

---

에러로그

```
android.content.res.Resources$NotFoundException: String resource ID #0x1f40
at android.content.res.Resources.getText(Resources.java:354)
at android.widget.TextView.setText(TextView.java:6133)
at org.androidtown.mybudgeter.pager.ExpenditureRecyclerAdapter.onBindViewHolder(ExpenditureRecyclerAdapter.java:30)
at org.androidtown.mybudgeter.pager.ExpenditureRecyclerAdapter.onBindViewHolder(ExpenditureRecyclerAdapter.java:16)
....      
```

```java
expenditureAmount.setText(currentExpenditure.getAmount());
```

에러로그는 위와 같은 코드에서 발생된 것으로, setText에 Integer 값을 전달했을 때 경우이다.    
Integer값을 String으로 변환해주면 에러가 수정된다.

```java
expenditureAmount.setText(Integer.toString(currentExpenditure.getAmount()));
```



