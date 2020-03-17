---
layout: post-content
title: (코딩인터뷰) 시스템 설계 및 규모 확장성 문제 4 - 캐시
date: 2019-08-30
categories: [codinginterview]
---

코딩 인터뷰 완전분석 (CRACKING THE CODING INTERVIEW 6/E) / 게일 라크만 맥도웰 지음 / 인사이트

---

# Q. 캐시 : 간단한 검색 엔진으로 구현된 웹 서버를 생각해보자. 이 시스템에선 100개의 컴퓨터가 검색 요청을 처리하는 역할을 하고 있다. 예를 들어 하나의 컴퓨터 집단에 processSearch(string query)라는 요청을 보내면 그에 상응하는 검색 결과를 반환해 준다. 하지만 어떤 컴퓨터가 요청을 처리하게 될지는 그때그때 다르며, 따라서 같은 요청을 한다고 같은 컴퓨터가 처리할 거라고 장담할 수 없다. processSearch 메서드는 아주 고비용이다. 최근 검색 요청을 캐시에 저장하는 메커니즘을 설계하라. 데이터가 바뀌었을 때 어떻게 캐시를 갱신할 것인지 반드시 설명하라.

## 가정을 통해 문제를 구체화하자

* 필요할 때 processSearch를 호출하는 것 이외에도, 모든 쿼리는 최초로 호출된 서버에서 처리된다.
* 캐시하고자 하는 쿼리의 수는 굉장히 크다(수백만 개)
* 서버 간 호출은 상대적으로 빨리 처리된다.
* 쿼리의 결과는 정렬된 URL 리스티이다. 각 원소에는 최대 50글자의 제목과 200글자의 요약문이 따라 붙는다.
* 가장 인기 있는 쿼리의 경우 항상 캐시에 보관되어 있다.

## 시스템 요구 사항을 정리하자

1. 최근 검색 요청을 캐시에 저장해야 한다.
2. 캐시를 통해 빠른 탐색이 가능해야 한다.
3. 쿼리 결과가 변경될 경우 캐시를 변경하거나 삭제할 수 있어야 한다.

## 단순하게 생각하자 - 요구사항 1, 2번

단순하게 컴퓨터가 하나일 경우로 가정하여 설계해본다.

빠른 탐색이 가능하려면 key-value 쌍의 자료구조인 해시맵을 사용하는 게 적절할 것 같다.     
쿼리를 해시 처리하여 key값을 생성하고 쿼리의 결과 값을 value로 해서 해시맵에 저장하면 될 듯 싶다.

첫번째 요구사항을 보면 캐시에 최신 검색 순으로 저장될 필요가 있다. 데이터를 순서대로 저장해야 한다.    
하지만 해시맵은 데이터의 순서를 기억하기에는 적절치 못한 자료구조다.    
순서를 위한 자료구조는 배열과 연결리스트가 있는데 최신 검색의 삽입과 오래된 검색의 삭제가 빈번하므로 연결리스트 자료구조가 적절하지 않을까?    
그러나,,,, 연결리스트는 탐색 시 Q(N) 시간이라 빠른 탐색이 힘들다..

어떻게 할까?    
두 자료구조를 합치면 어떨까?  

이는 LRU 캐시를 구현하라는 것과 같다.    
LRU는 OS의 페이지 교체 알고리즘의 하나로 최근에 가장 오랫동안 사용되지 않은 페이지를 교체하는 기법이다.
다음은 LRU 캐시 구현 그림이다.    

![1](/assets/images/2019-08-30-img/4-1.jpg)    <br/> 

## 이제 현실로 돌아와서 생각하자 - 여러 서버로 확장

**방법1 - 각 서버에 별도의 캐시를 둔다**

서버 1에 같은 쿼리를 2번 보내면, 두 번째 처리 결과는 캐시에서 가져온다.    
그러나 서버 1에 보냈다가 서버 2에 보내면 서버 2는 해당 쿼리를 새로운 쿼리로 처리한다.   
* 장점 : 서버 간 통신이 필요없기에 상대적으로 빠르다.
* 단점 : 같은 쿼리가 반복되도 새로운 쿼리로 인식하기 때문에 최적화를 위한 방법으로 부적절

**방법2 - 각 서버에 캐시 복사본을 둔다**

각 서버에 전체 캐시의 완전한 복사본을 유지하는 방법이다.    
새로운 데이터가 캐시에 추가되는 순간 그 데이터는 모든 서버로 보내진다.    
따라서 연결리스트와 해시테이블을 비롯한 모든 자료구조가 중복되어 저장된다.
* 장점 : 어느 서버에서도 동일하게 존재하기 때문에 빈번하게 사용되는 쿼리와 실행 결과는 항상 캐시 내에 존재한다.
* 단점1 : 캐시를 갱신할 때마다 데이터를 N개의 서로 다른 서버로 전송해야 한다는 점
* 단점2 : 각 캐시를 저장하기 위해 N배 더 큰 공간이 필요하므로 캐시에 저장 가능한 항목의 수가 줄어든다.

**방법3 - 각 서버에 캐시의 일부를 저장한다**

캐시를 분할하여 각 서버에 그 일부만을 보관한다.    
예를 들어, 서버 i가 어떤 쿼리에 대한 결과를 알고 싶다고 하자.  

![1](/assets/images/2019-08-30-img/4-2.jpg)    <br/> 

## 요구 사항 3번을 해결하자

* 요구사항 3 : 쿼리 결과가 변경될 경우 캐시를 변경하거나 삭제할 수 있어야 한다.

캐시가 충분히 클 경우 어떤 쿼리는 너무 빈번해서 항상 캐시에 남아 있을 수 있다.    
따라서 주기적으로 혹은 어떤 쿼리 결과가 변경되었을 때마다 캐시에 보관된 결과를 갱신할 수 있는 방법이 필요하다.

쿼리의 결과가 바뀌는 순간
1. URL이 가리키는 페이지 내용이 바뀔 때(URL이 가리키는 페이지가 삭제되었을 때)
2. 페이지의 랭킹이 바뀌어서 결과의 순서가 변경될 때
3. 특정한 쿼리에 관련있는 새로운 페이지가 등장할 때

방법
1. 데이터가 수정되었을 때 곧바로 캐시를 갱신할 필요가 없다. ->각 서버에 저장된 캐시를 주기적으로 탐색한 뒤 갱신된 URL에 대해서는 캐시 결과를 비운다.
2. X분이 지나면 자동으로 캐시가 버려지도록 한다.


## 관련문제

[카카오 코딩테스트 문제](https://tech.kakao.com/2017/09/27/kakao-blind-recruitment-round-1/).

## 캐시(난이도: 하)
지도개발팀에서 근무하는 제이지는 지도에서 도시 이름을 검색하면 해당 도시와 관련된 맛집 게시물들을 데이터베이스에서 읽어 보여주는 서비스를 개발하고 있다. <br/>
이 프로그램의 테스팅 업무를 담당하고 있는 어피치는 서비스를 오픈하기 전 각 로직에 대한 성능 측정을 수행하였는데, 제이지가 작성한 부분 중 데이터베이스에서 게시물을 가져오는 부분의 실행시간이 너무 오래 걸린다는 것을 알게 되었다.<br/>
어피치는 제이지에게 해당 로직을 개선하라고 닦달하기 시작하였고, 제이지는 DB 캐시를 적용하여 성능 개선을 시도하고 있지만 캐시 크기를 얼마로 해야 효율적인지 몰라 난감한 상황이다.<br/>

어피치에게 시달리는 제이지를 도와, DB 캐시를 적용할 때 캐시 크기에 따른 실행시간 측정 프로그램을 작성하시오.

### 입력 형식
- 캐시 크기(cacheSize)와 도시이름 배열(cities)을 입력받는다.
- cacheSize는 정수이며, 범위는 0 ≦ cacheSize ≦ 30 이다.
- cities는 도시 이름으로 이뤄진 문자열 배열로, 최대 도시 수는 100,000개이다.
- 각 도시 이름은 공백, 숫자, 특수문자 등이 없는 영문자로 구성되며, 대소문자 구분을 하지 않는다. 도시 이름은 최대 20자로 이루어져 있다.

### 출력 형식
입력된 도시이름 배열을 순서대로 처리할 때, “총 실행시간”을 출력한다.

### 조건
- 캐시 교체 알고리즘은 LRU(Least Recently Used)를 사용한다.
- cache hit일 경우 실행시간은 1이다.
- cache miss일 경우 실행시간은 5이다.

### 입출력 예제

<table>
<thead>
	<tr><th>캐시크기</th><th>도시이름</th><th>실행시간</th></tr>
</thead>
<tbody>
	<tr>
    <td>3	</td><td>[“Jeju”, “Pangyo”, “Seoul”, “NewYork”, “LA”, “Jeju”, “Pangyo”, “Seoul”, “NewYork”, “LA”]	</td><td>50</td>
    </tr>
    <tr>
<td>3	</td><td>[“Jeju”, “Pangyo”, “Seoul”, “Jeju”, “Pangyo”, “Seoul”, “Jeju”, “Pangyo”, “Seoul”]</td>	<td>21</td>
</tr>
<tr>
<td>2	</td><td>[“Jeju”, “Pangyo”, “Seoul”, “NewYork”, “LA”, “SanFrancisco”, “Seoul”, “Rome”, “Paris”, “Jeju”, “NewYork”, “Rome”]</td><td>	60</td>
</tr>
<tr>
<td>5</td><td>	[“Jeju”, “Pangyo”, “Seoul”, “NewYork”, “LA”, “SanFrancisco”, “Seoul”, “Rome”, “Paris”, “Jeju”, “NewYork”, “Rome”]</td><td>	52</td>
</tr>
<tr>
<td>2</td><td>	[“Jeju”, “Pangyo”, “NewYork”, “newyork”]</td>	<td>16</td>
</tr>
<tr>
<td>0	</td><td>[“Jeju”, “Pangyo”, “Seoul”, “NewYork”, “LA”]	</td><td>25</td>
</tr>
</tbody>
</table>

```java
import java.util.*;
public class Cache {
	private int cacheSize;
	private HashMap<String, Node> map;
	private LinkedList<Node> list;
	private int time;

	public Cache(int cacheSize) {
		this.cacheSize = cacheSize;
		map = new HashMap<String, Node>();
		list = new LinkedList<Node>();
		time = 0;
	}

	public void insertResults(String city) {
		city = city.toLowerCase();
		if (map.containsKey(city)) {
			time+=1;
			Node node = map.get(city);
			list.remove(node);
			list.addFirst(node);
			return;
		}
		
		time+=5;
		Node node = new Node(city);
		map.put(city, node);
		list.addFirst(node);
		
		if (list.size() > cacheSize) {
			Node lastNode = list.removeLast();
			map.remove(lastNode.cityName);
		}
	}
	
	public void printCache() {
		Iterator<Node> i = list.iterator();
		while (i.hasNext()) {
			System.out.print(i.next().cityName + " ");
		}
	}
	
	public int getTime() {
		return time;
	}

	class Node {
		String cityName;
		Node next;

		public Node(String cityName) {
			this.cityName = cityName;
			this.next = null;
		}
	}


}
```

```java
public class LRUQuestion {
	public static void main(String[] args) {
		
		String[] cities1 = {"Jeju", "Pangyo", "Seoul", "NewYork", "LA", "Jeju", "Pangyo", "Seoul", "NewYork", "LA"};
		System.out.println("실행시간 : " + testCache(3,cities1));
		String[] cities2 = {"Jeju", "Pangyo", "Seoul", "Jeju", "Pangyo", "Seoul", "Jeju", "Pangyo", "Seoul"};
		System.out.println("실행시간 : " + testCache(3,cities2));
		String[] cities3 = {"Jeju", "Pangyo", "Seoul", "NewYork", "LA", "SanFrancisco", "Seoul", "Rome", "Paris", "Jeju", "NewYork", "Rome"};
		System.out.println("실행시간 : " + testCache(2,cities3));
		String[] cities4 = {"Jeju", "Pangyo", "Seoul", "NewYork", "LA", "SanFrancisco", "Seoul", "Rome", "Paris", "Jeju", "NewYork", "Rome"};
		System.out.println("실행시간 : " + testCache(5,cities4));
		String[] cities5 = {"Jeju", "Pangyo", "NewYork", "newyork"};
		System.out.println("실행시간 : " + testCache(2,cities5));
		String[] cities6 = {"Jeju", "Pangyo", "Seoul", "NewYork", "LA"};
		System.out.println("실행시간 : " + testCache(0,cities6));
	}
	
	public static int testCache(int cacheSize, String[] cities) {
		Cache cache = new Cache(cacheSize);
		for (String city : cities)
			cache.insertResults(city);
		
		return cache.getTime();
	}
}
```