# Java Comparator과 Comparable

- Title : Java Comparator과 Comparable
- Date : 2019-10-18
- Category: Java

PS 문제를 풀다가 Arrays.sort()와 Collections.sort()를 오름차순(디폴트)이 아닌 다른 기준으로 정렬하고 싶을 때가 있었다. 찾아보니 Comparator 또는 Comparable을 사용하면 된다고 한다. 음.. 알아본 김에 잊어버리지않게 기록을 해야겠다!

# 컬렉션을 입맛대로 정렬하고 싶다면?

내맘대로 컬렉션의 정렬 기준을 정하고 싶다면 `Comparator`과 `Comparable` 인터페이스를 사용하면 된다.

```java
import java.util.Comparator;
public interface Comparator {
    int compare(Object o1, Object o2);
    boolean equals(Object obj);
}
```

<br/>

```java
import java.lang.Comparable;
public interface Comparable {
    public int compareTo(Object o); // 양수 : 내림차순, 음수 : 오름차순
}
```

# 그럼 둘의 차이는 무엇일까?

Comparable을 구현한 클래스들은 기본적으로 오름차순으로 정렬되도록 구현되어있다.  
Integer, Character와 같은 `wrapper` 클래스(int, long, float, double 등을 객체화한 클래스)와 `String, Date, File`과 같은 것이 Comparable을 구현한 대표적 클래스이다. 그 외 Java API 문서에서 클래스 목록을 확인할 수 있다.  
그래서 우리가 `Arrays.sort(정렬대상)` 로 오름차순 정렬을 할 수 있는 것이다. 아하!

```java
public final class Integer extends Number implements Comparable {
    ...
    public int compareTo(Object o) {
        return compareTo((Integer) o);
    }

    public int compareTo(Integer anotherInteger) {
        int thisVal = this.value;
        int anotherVal = anotherInteger.value;
        return (thisVal < anotherVal ? -1 : (thisVal ==anotherVal ? 0 : 1));
    }
}
```

실제 Integer 클래스의 일부이다.  
보이는 것 처럼 Comparable 인터페이스를 구현하고 compareTo 메서드에서 오름차순으로 정렬되도록 정의해놓았다.

Comparator 인터페이스는 Comparable로 구현된 클래스를, 또는 커스텀 클래스를 기본 정렬 기준 외에 다른 기준으로 정렬하고자 할 때 사용하면 된다.  
아래는 실제 예제로 백준 16236 아기 상어 문제를 풀 때 우선순위큐의 정렬기준을 Comparator을 이용해 정의한 것이다.

```java
PriorityQueue<Point> pq = new PriorityQueue<>(new Comparator<Point>() {
	@Override
	public int compare(Point o1, Point o2) {
		if (o1.dis != o2.dis) return o1.dis-o2.dis;
		if (o1.row != o2.row) return o1.row-o2.row;
		return o1.col-o2.col;
	}
});
```

또는 책의 예제를 보면 따로 클래스를 정의해서 사용할 수 있다.  
이 예제는 Comparable을 구현한 클래스 String의 정렬 방식을 내림차순으로 바꾸는 방법이다.  
단순히 -1을 곱해 반대의 값을 반환하게 구현했다.

```java

Arrays.sort(strArr, new Descending());

Class Descending implements Comparator {
    public int compareTo(Object o1, Object o2) {
        if (o1 instanceof Comparable && o2 instanceof Comparable) {
            Comparable c1 = (Comparable) o1;
            Comparable c2 = (Comparable) o2;
            return c1.compareTo(c2) * -1;
        }
    }
}
```

이제 내맘대로 정렬할 수 있게 되었담!!!!

자바의 정석 책을 참고했습니다.
