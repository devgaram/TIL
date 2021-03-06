# Union-Find란?

- Title : Union-Find란?
- Date : 2020-02-27
- Category: Algorithm

## Why?

[1717번: 집합의 표현](https://www.acmicpc.net/problem/1717)

오 쉽네?😁 라고 생각하며 빠르게 풀고 제출했지만 **메모리 초과**가 발생했다. 아무래도 n\*n 이차원 배열을 자료구조로 선택한 것이 원인인 듯 싶어 일차원 배열로 바꿔서 다시 풀어봤다. n크기의 배열에서 0→n 까지 반복을 돌려서 v[i]가 b면 a로 값을 바꿔주는 식으로 했다. 이번엔 메모리 초과는 안 떴지만 **시간 초과**가 떴다..두둥 😢🤢 어떻게 풀어야 하는 거지?

## Union-Find란?

> 여러 노드가 존재할 때 선택한 두 개의 노드가 같은 그래프에 속하는 지 판별할 때 사용하는 대표적인 그래프 알고리즘으로 합집합 찾기라는 의미를 가진다.

Disjoint Set(디스조인트 셋)을 표현할 때 사용하는 자료구조로 **공통 원소가 없는 부분 집합**들로 나눠진 원소들에 대한 정보를 저장하는 자료구조다. 예를 들어 {1}, {2,3} , {4,5,6}, {7} 와 같이 공통 원소가 없는 부분 집합을 저장할 때 사용한다. 이 상황을 표현하기 위해 초기화, Union(합치기) 연산과 Find(찾기) 연산을 지원해야해서 Union-Find라고 부르게 되었다.

- 초기화: N개의 원소가 각각의 집합에 포함되어 있도록 초기화한다. 예시) N=5면 {0}, {1}, {2}, {3}, {4}, {5} 로 초기화
- Union연산: 두 원소 a, b가 주어질 때, 이들이 속한 두 집합을 하나로 합친다.
- Find연산: 어떤 원소 a가 주어질 때, 이 원소가 속한 집합을 반환한다.

## Union-Find 구현

> 실제 구현은 주로 트리구조를 이용한다. 배열은 왜 사용하지 않는지? 트리를 사용할 때의 장점은 무엇인지? 알아보자

### 배열로 표현

1차원 배열로 집합을 표현한다. 예를 들어 Array[i]: i번 원소가 속하는 집합의 번호 라 해보자.

- 초기화: Array[i] = i 로 각각 다른 집합 번호로 초기화한다.
- Union연산 O(N): 두 집합을 합치기 위해 배열의 모든 원소를 순회하면서 하나의 집합 번호를 나머지 한 개의 집합 번호로 교체한다. 예를 들어 1 2 3 4 5 배열에서 3번 집합을 2번 집합으로 합치면 1 2 2 4 5가 된다. 다시 2번 집합을 1번 집합으로 합친다면 1 1 1 4 5가 된다.
- Find연산 O(1): Array[i] 값이므로 한 번에 원소가 속하는 집합의 원소를 알 수 있다.

배열로 Union-Find를 구현할 수는 있다. 하지만, Union연산의 시간복잡도는 O(N)으로 N이 커지면 백준 1717문제처럼 시간초과가 날 것이다. 그럼 트리구조를 사용한다면 **맞습니다!** 를 볼 수 있을까?

### ✨트리로 표현하기

- 한 집합에 속하는 원소들은 하나의 트리로 묶인다. 자료구조는 아래 그림과 같이 트리들의 집합으로 표현된다.

![Union%20Find/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Algorithm/images/2020-02-27-img/Untitled.png)

- 트리의 루트 노드가 각 원소가 속한 집합의 번호가 된다.
- Union 연산을 수행하기 전 두 원소가 같은 집합에 속하는 지 확인한다. 다른 집합에 속했을 때만 Union 연산을 수행한다. 즉, 두 원소의 루트 노드가 다르면 Union 연산을 수행한다.
- Find 연산을 위해서는 모든 자식 노드가 부모에 대한 포인터 정보를 가지고 있어야 한다. 이렇게 해야 포인터를 따라 올라가 루트 노드를 찾을 수 있게 된다. 단, 부모 노드에서 자식 노드로 내려가는 일은 발생하지 않기 때문에 부모가 자식에 대한 포인터 정보를 가질 필요는 없다.

**구현 방법**

- 초기화: N개의 루트 노드를 생성하고 자기 자신을 가리키는 포인터를 갖도록 설정한다. 각각의 노드가 루트 노드가 된다.
- Union연산: 각 트리의 루트를 찾은 뒤 루트 노드가 다르면 하나를 다른 한 쪽의 자손으로 넣어 두 트리를 합친다. 시간 복잡도는 루트 노드를 찾기 위해 Find연산을 수행하므로 Find연산 수행 시간이 지배한다.

![Union%20Find/Untitled%201.png](https://raw.githubusercontent.com/devgaram/TIL/master/Algorithm/images/2020-02-27-img/Untitled%201.png)

- Find연산: 각 노드에 저장된 포인터 정보를 따라 주어진 원소가 포함된 트리의 루트 노드를 찾는다. 트리의 높이와 시간 복잡도가 비례한다.

## ✔백준 문제 풀이

```cpp
    #include <iostream>
    #include <string>
    using namespace std;

    int s[1000001];
    int n;

    int find(int a) {
    	if (s[a] == a) return a;
    	return s[a] = find(s[a]);
    }

    void fnc_union(int a, int b) {
    	if (a == b) return;
    	a =	find(a);
    	b = find(b);
    	if (a == b) return;
    	s[b] = a;
    }


    int main() {
    	int m;
    	scanf("%d %d", &n, &m);

    	for (int i=0; i<n+1; i++) {
    		s[i] = i;
    	}
    	while (m--) {
    		int t, a, b;
    		scanf("%d %d %d", &t, &a, &b);
    		if (t) {
    			if (find(a) == find(b)) printf("YES\n");
    			else printf("NO\n");
    		}
    		else fnc_union(a, b);
    	}
    	return 0;
    }
```

## 👏최적화하기

트리 구조는 최악의 경우 완전히 비대칭적인 트리 즉, 연결리스트 형태가 된다. 예를 들어 두 트리 a, b를 합칠 때 항상 a의 루트 노드에 b의 루트 노드를 자손으로 합친다면 아래 그림과 같이 원소의 개수가 N개일 때, 트리의 높이가 N-1인 연결 리스트 형태가 된다. 최악의 경우 Union, Find 연산의 시간복잡도는 O(N)이 된다는 얘기다.

![Union%20Find/Untitled%202.png](https://raw.githubusercontent.com/devgaram/TIL/master/Algorithm/images/2020-02-27-img/Untitled%202.png)

해결 방법💊은 트리의 높이가 낮은 트리의 루트 노드에 합치는 것이다. 이렇게 하면 트리의 높이가 크게 높아지는 상황을 방지할 수 있다. 이러한 최적화를 Union-By-Rank 라고 하며 여기서 rank는 트리의 높이를 저장한다.

**구현방법**

- 두 트리를 합칠 때 랭크(높이)가 더 낮은 트리에 합친다.
- 랭크에는 트리의 높이를 저장한다.
- 두 트리의 랭크가 동일한 경우에 합쳐진 트리의 랭크를 1 증가시킨다.

> 최적화 과정을 거치면 트리의 랭크는 합쳐진 두 트리의 랭크가 같을 때만 증가한다. 즉 높이 h인 트리는 높이 h-1인 트리 두개가 합쳐져야한다. 높이 h-1인 트리가 최소 x개의 노드를 가져야한다면 높이가 h가 되기 위해서는 2x개의 노드가 필요하다. 최적화를 통해 트리의 높이가 트리에 속한 **노드의 수의 로그에 비례**하는 것을 보장할 수 있게된다.

백준 1717 최적화 코드

```cpp
    #include <iostream>
    #include <string>
    using namespace std;

    int s[1000001];
    int r[1000001] = {1};
    int n;

    int find(int a) {
    	if (s[a] == a) return a;
    	return s[a] = find(s[a]); // 경로 압축 최적화
    }

    void fnc_union(int a, int b) {
    	if (a == b) return;
    	a =	find(a);
    	b = find(b);
    	if (a == b) return;
    	// 랭크 최적화 적용
    	if (r[a] > r[b]) swap(a, b);
    		s[a] = b;
    	if (r[a] == r[b]) r[b]++;
    }


    int main() {
    	int m;
    	scanf("%d %d", &n, &m);

    	for (int i=0; i<n+1; i++) {
    		s[i] = i;
    	}
    	while (m--) {
    		int t, a, b;
    		scanf("%d %d %d", &t, &a, &b);
    		if (t) {
    			if (find(a) == find(b)) printf("YES\n");
    			else printf("NO\n");
    		}
    		else fnc_union(a, b);
    	}
    	return 0;
    }
```

**경로 압축(Path Compression)**

각 원소의 부모 노드를 재귀를 통해 찾아낸 루트 노드로 바꾸면 다음번 동일한 Find연산 수행 시 경로를 따라갈 필요없이 바로 루트를 찾을 수 있다. 재귀적인 구현으로 a에서 루트까지 올라가는 모든 경로 상에 있는 노드들에게 경로 압축 최적화가 자동으로 수행된다.

```cpp
    int find(int a) {
    	if (s[a] == a) return a;
    	return s[a] = find(s[a]); // 경로 압축 최적화
    }
```

![Union%20Find/Untitled%203.png](https://raw.githubusercontent.com/devgaram/TIL/master/Algorithm/images/2020-02-27-img/Untitled%203.png)

위 최적화 과정을 모두 적용하면 연산 수행 시간을 분석하기 어려울 수 있다. 트리의 높이 변화에 따라 Find연산 수행 시간이 달라지기 때문이다. 평균 수행시간은 O(a(N))으로 a(N)은 에커만 함수를 이용해 정의되는 함수다. 거의 모든 크기의 N에 대해 4 이하의 값을 가져서 모든 입력에 대해 상수 시간에 동작한다고 봐도 무관하다.

**관련 문제**

[Disjoint-set](https://www.acmicpc.net/problem/tag/Disjoint-set)

**참고 99.9%**

[[자료구조]Union-Find: Disjoint Set의 표현](https://bowbowbow.tistory.com/26)
