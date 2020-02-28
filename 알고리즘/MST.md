# MST(최소신장트리)

- 알고리즘
- 2020년 02월 28일

## Why?

[1922번: 네트워크 연결](https://www.acmicpc.net/problem/1922)

이 문제를 풀면서 MST 이론을 정리해놔야 겠다는 생각이 들었다.

## MST(Minimun spanning tree)란?

신장 트리는 그래프 내의 모든 정점을 포함하는 트리다. 트리의 특수한 형태로 **모든 정점들이 연결**되어 있어야 하고 사이클을 포함해서는 안된다. 따라서 신장 트리는 그래프에 있는 n개의 정점을 정확히 n-1개의 간선으로 연결하게 된다.

하나의 그래프에는 많은 신장 트리가 존재할 수 있는 데 이 중 사용된 간선들의 **가중치 합이 가장 최소인 신장 트리**가 최소 신장 트리가 된다.

## 신장 트리 찾기

DFS나 BFS를 사용하여 탐색 도중에 사용된 간선을 모으면 신장 트리를 만들 수 있다.

**DFS와 큐로 신장트리 찾기**

    #include <iostream>
    #include <vector>
    #include <queue>
    #define MAX_NODE 20
    using namespace std;
    
    vector<int> graph[MAX_NODE]; // 인접 리스트로 구현한 그래프
    bool visited[MAX_NODE] = {false}; // 방문 표시
    queue<int> st; // 신장 트리
    
    void dfs(int v) { // 시작 노드
    	visited[v] = true;
    	st.push(v);
    	for (int i=0; i<graph[v].size(); i++) {
    		if (!visited[graph[v][i]]) {
    			dfs(graph[v][i]);
    		}
    	}
    }

## 최소 신장 트리 찾기

> 구하는 방법으로는 Kruskal과 Prime이 제안한 알고리즘이 대표적이다. 이 알고리즘들은 최소 신장 트리가 간선의 가중치의 합이 최소이어야 하고, 반드시 n-1개의 간선만 사용해야 하며, 사이클이 포함되어서는 안 된다는 조건들을 적절히 이용한다.

### Kruskal의 MST 알고리즘

이 알고리즘은 **탐욕적인 방법**을 이용한다. 탐욕적인 방법은 결정을 해야 할때마다 그 순간에 가장 최적이라고 생각되는 것을 선택함으로써 최종적인 해답을 구한다. 최적을 선택했다고 항상 최적의 해답을 주는 건 아니지만 다행히 Kruskal 알고리즘은 최적의 해답을 주는 것으로 증명되어 있다.

- 그래프 e개의 간선들을 가중치의 오름차순으로 정렬한다.
- 정렬된 간선 리스트에서 순서대로 사이클을 형성하지 않는 간선을 선택하여 현재의 최소 비용 신장 트리의 집합에 추가한다. 만약 사이클을 형성하면 그 간선은 제외된다.
- 선택된 간선의 개수가 정점의 개수보다 하나 적으면 알고리즘을 종료한다.

**사이클 체크**

간선을 집합에 추가할 때 사이클을 생성하는지를 체크해야 한다. 양 끝 정점이 서로 다른 집합에 속하는 경우 두 정점을 연결하여도 사이클이 형성되지 않는다. Union-Find 알고리즘을 이용하여 간선의 양끝 정점이 같은 집합에 속해 있는지를 검사할 수 있다. 

**시간복잡도**

Union-Find 알고리즘을 이용하면 Kruskal 알고리즘의 시간 복잡도는 간선들을 정렬하는 시간에 좌우된다. 따라서 간선 e개를 퀵 정렬과 같은 효율적인 알고리즘으로 정렬한다면 시간복잡도는 **O(elog₂e)** 다.

✔**백준 문제 풀이**

1922번 문제를 Kruskal 알고리즘을 사용한 풀이

    #include <iostream>
    #include <vector>
    #include <algorithm>
    
    using namespace std;
    
    struct Info {
    	int a;
    	int b;
    	int c;
    };
    
    int n;
    int parent[1001]; // 부모 노드
    int num[1001]; // 집합의 크기
    
    void set_init(int n) {
    	for (int i=1; i<n+1; i++) {
    		parent[i] = i;
    		num[i] = 1;
    	}	
    }
    int set_find(int v) {
    	if (parent[v] == v) return v;
    	return parent[v] = set_find(parent[v]);
    }
    
    bool set_union(int a, int b) {
    	a = set_find(a);
    	b = set_find(b);
    	
    	if (a == b) return false;
    	
    	if (num[a] > num[b]) swap(a, b);
    	parent[a] = b;
    	if (num[a] == num[b]) num[b]++;
    	return true;
    }
    
    bool compare(Info t1, Info t2) {
    	return t1.c < t2.c;
    }
    int main() {
    	int m, a, b, c;
    	scanf("%d", &n);
    	scanf("%d", &m);
    	vector<Info> edge;
    	
    	while (m--) {
    		scanf("%d %d %d", &a, &b, &c);
    		edge.push_back({a, b, c});
    	}
    	
    	sort(edge.begin(), edge.end(), compare);
    	
    	set_init(n);
    	
    	int count = 0, cost = 0;
    	int ra, rb;
    	for (int i=0; i<edge.size(); i++) {
    		if (set_union(edge[i].a, edge[i].b)) {
    			cost += edge[i].c;
    			if(++count == n-1) break;
    		}
    	}
    	
    	printf("%d", cost);	
    	
    	return 0;
    }

✔**응용 문제**

백준 4195번

[4195번: 친구 네트워크](https://www.acmicpc.net/problem/4195)

    #include <iostream>
    #include <string>
    #include <map>
    using namespace std;
    
    map<string, string> parent;
    map<string, int> num;
    
    void check_init(string v) {
    	if (parent.find(v) == parent.end()) {
    		parent.insert(make_pair(v, v));
    		num.insert(make_pair(v, 1));
    	}
    }
    string set_find(string v) {
    	if(parent[v] == v) return v;
    	return parent[v] = set_find(parent[v]);
    }
    
    int set_union(string a, string b) {
    	a = set_find(a);
    	b = set_find(b);
    	if (a == b) return num[a];
    	if (num[a] > num[b]) swap(a, b);
    	parent[a] = b;
    	num[b] += num[a];
    	return num[b];
    }
    int main() {
    	ios::sync_with_stdio(false);
    	cin.tie(0);
    	int t, f;
    	cin >> t;
    	
    	while(t--) {
    		cin >> f;
    		while (f--) {
    			string a, b;
    			cin >> a;
    			cin >> b;
    			
    			check_init(a);
    			check_init(b);
    			
    			cout << set_union(a, b) << "\n";
    		}
    		parent.clear();
    		num.clear();
    	}
    	
    	return 0;
    }

> 다른 사람 풀이보다 시간이 더 걸려서 비교해보니 map<string, int>로 기존 Union-Find 알고리즘을 그대로 사용할 수 있게 이름에 index를 강제 할당시키는 방식이었다. 왜 이런 생각을 못했지.....😢😢 아! 그리고 **ios::sync_with_stdio(false); cin.tie(0);** 를 안했더니 시간 초과가 났었다.

### Prim의 MST 알고리즘

Kruskal은 간선 선택을 기반으로 하는 알고리즘인 반면에, Prim의 알고리즘은 **정점 선택**을 기반으로 하는 알고리즘이다. 또한 Kruskal의 알고리즘은 이전 단계에서 만들어진 신장 트리와는 상관없이 무조건 최소 간선만을 선택하는 방법인 데 반하여 Prime의 알고리즘은 이전 단계에서 만들어진 신장 트리를 확장하는 방식이다.

- 시작 단계에는 시작 정점만이 신장 트리 집합에 포함된다.
- 앞 단계에서 만들어진 신장 트리 집합에 속한 정점들의 인접 정점들 중에서 최소 비용 간선으로 연결된 정점을 선택하여 신장 트리 집합에 포함한다.
- 트리 집합의 정점 개수가 n개가 될 때까지 이 과정을 반복한다.

아래 그림으로 예를 들어보자.

- 우선 A 정점을 신장 트리 집합에 포함시킨다.
- A 정점의 인접 정점인 D, B와 연결된 간선 중 가중치가 최소인 D를 선택하여 집합에 포함시킨다.
- 신장 트리 집합의 정점 개수가 2개{A, D}로 아직 7개가 아니므로 다시 A, D의 인접 정점을 확인한다.
- A-B, D-B, D-E, D-F 간선 중 가중치가 최소인 정점인 F를 선택하여 집합에 포함시킨다.
- 반복..

![MST/Untitled.png](MST/Untitled.png)

**구현 방법**

- dist[정점개수] 배열이 필요하다. dist는 현재 신장 트리의 정점 집합에서 각 정점까지의 거리를 가지고 있다. A(0)가 시작 정점이면 dist[0]만 값이 0이고 나머지 배열의 값은 무한대를 가진다. 명백하게 처음에는 트리 집합에 아무것도 없으므로 당연하다. 정점들이 트리 집합에 추가되면서 dist 값은 변경된다.
- 우선순위 큐가 필요하다. 큐에 모든 정점을 삽입하는 데 이 때 우선순위는 dist 배열 값이 된다.
- while 루프로 큐에서 가장 작은 dist 값을 가지는 정점을 추출한다. 추출된 정점이 트리 집합에 추가된다.
- 트리 집합에 새로운 정점 u가 추가되었으므로 u에 인접한 정점 v들의 dist 값을 변경시켜준다. 즉, 기존의 dist[v] 값보다 간선(u, v)의 가중치가 적으면 간선 (u, v)의 가중치로 dist[v]를 변경시킨다.
- 큐가 빌 때까지 반복한다.

**시간복잡도**

Prim의 알고리즘은 배열을 사용하면 주 반복문이 정점의 수 n만큼 반복하고, 내부 반복문이 n번 반복하므로 Prim의 알고리즘의 시간 복잡도는 O(n²)다. 하지만 우선 순위 큐를 사용하면 **O(elongv)** 의 시간복잡도를 가진다.

✔**백준 문제 풀이**

1197번 문제를 Prim의 알고리즘을 사용한 풀이

[1197번: 최소 스패닝 트리](https://www.acmicpc.net/problem/1197)

    #include <iostream>
    #include <vector>
    #include <queue>
    using namespace std;
    
    bool selected[10001] = {false}; // selected[i]: true면 mst 집합의 원소, false면 선택 가능
    vector<pair<int, int> > edge[100001]; // 인접 리스트로 그래프 자료구조 구현
    
    int prim() {
    	int cost = 0;
    	// 우선 순위 큐: 가중치가 적은 정점이 우선 순위로 나오게
    	priority_queue<pair<int, int>, vector<pair<int, int> >, greater<pair<int, int> > > pq;
    	pq.push(make_pair(0, 1)); // 가중치:0, 정점: 1
    	
    	while (!pq.empty()) {
    		pair<int, int> p = pq.top();
    		pq.pop();
    		if (selected[p.second]) continue; // 이미 선택된 정점이면 작업할 필요가 없다.
    		selected[p.second] = true;
    		cost += p.first;
    		for (int i=0; i<edge[p.second].size(); i++) {
    			if (!selected[edge[p.second][i].second]) {
    				pq.push(edge[p.second][i]);
    			}
    		}
    	}
    	return cost;
    	
    }
    int main() {
    	ios::sync_with_stdio(false);
    	cin.tie(0);
    	
    	int v, e;
    	cin >> v >> e;
    	int t = e;
    	while (t--) {
    		int a, b, c;
    		cin >> a >> b >> c;
    		edge[a].push_back(make_pair(c, b));
    		edge[b].push_back(make_pair(c, a));	
    	}
    	
    	printf("%d", prim());
    	
    	return 0;
    }

> 우선 순위 큐에 전달할 데이터가 2개 이상일 때, pair로 묶으면 first 값을 1순위, second 값을 2순위로 우선 순위를 정할 수 있다.

**정리**

Kruskal 알고리즘은 희박한 그래프를 대상으로 할 경우에 유리하고 밀집한 그래프의 경우에는 Prim의 알고리즘이 적합하다라고 할 수 있다. 밀집 그래프는 간선이 많이 존재하는 그래프, 희소 그래프는 적은 숫자의 간선을 가지는 그래프다.

**참고**

[C언어로 쉽게 풀어쓴 자료구조](http://www.yes24.com/Product/Goods/69750539)