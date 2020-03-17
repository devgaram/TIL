# 최단 경로 알고리즘 - Bellman-Ford

- 알고리즘
- 2020년 03월 06일

## Why?

[11657번: 타임머신](https://www.acmicpc.net/problem/11657)

다익스트라가 통하지 않았다...😫**음수 가중치** 가 있으면 벨만-포드 알고리즘을 써야한다네...?

## 음수 가중치가 있을 때, 다익스트라가 통하지 않는다. 왜?

다익스트라는 선택된 정점의 집합 S에 없는 정점 중 가중치 값이 가장 작은 정점을 뽑는다. 단 아래의 상황이 만족되는 경우에 적용이 가능하다.

![Bellman%20Ford/Untitled.png](Bellman%20Ford/Untitled.png)

- 다익스트라에 따르면,  선택된 정점의 집합 S = {1} 일 때, 선택되지않은 정점 {2, 3, 4} ( int[] distance = [0, 5, INF, 6] )중 가장 가중치가 적은 2를 선택한다. 1 → 2는 1→ 4 → 2, 1→ 4 → 3 → 2 보다 적은 가중치를 갖는 것이 증명되기 때문이다.

**그러나, 음수 가중치가 있다면?**

![Bellman%20Ford/Untitled%201.png](Bellman%20Ford/Untitled%201.png)

- 다익스트라에 따르면, 집합 S = {1} 이고 distance가 [0, 5, INF, 6] 이므로 정점 2를 선택한다. 그러나, 1 → 2 가 1 → 4 → 2, 1 → 4 → 3 → 2 보다 적은 가중치를 가지나? **NO!** 음수 가중치에 의해 1에서 2의 최단 경로는 1 → 4 → 3 → 2 가 된다.

그럼 새로운 거리 가중치가 현재의 distance 보다 작으면 바꿀 수 있게 **집합 S에 속한 경우도 탐색하면 되지 않을까?**

- S = {1} , distance = [~~0~~, **5**, INF, 6]
- S = {1, 2}, distance = [~~0~~, **5**, 9, 6] —> 또 정점 2가 선택되어 **무한루프**를 타버린다.

그러면 **선택된 정점의 현재 distance가 이전에 선택되었을 때의 distance 값과 동일하다면 제외하면 되지 않을까?**

- S = {1, 2}, distance = [~~0~~, 5, 9, **6**]  —> 정점 2는 이전에 선택되었을 때와 distance가 같아서 제외하고 그 다음으로 정점 4를 선택
- S = {1, 2, 4}, distance = [~~0~~, **2**, 4, 6] —> 이전 distance 5보다 적은 가중치이므로 정점 2를 선택
- S = {1, 2, 4}, distance = [~~0~~, 2, **4**, 6] —> 정점 2는 이전 distance와 같으니깐 정점 3 선택
- S = {1, 2, 3, 4}, distance = [~~0~~, **1**, 4, 6] —> 정점 2 선택
- S = {1, 2, 3, 4}, distance = [~~0~~, 1, 4, 6] —> 모두 이전 distance와 같은 상태..

기존 다익스트라보다 복잡하다. **음수 가중치**가 있으면 벨만 포드 알고리즘을 써서 좀 더 간단하게 풀자!

## 벨만 포드 알고리즘

정점 u, v 사이의 최단 경로를 구할 때 그래프 내 **모든 간선에 대해 간선 경감(Edge Relaxation)을 수행**한다. u, v 사이의 최단 경로는 u → v 직접 간선일 수도 있고 u → v를 제외한 모든 노드 → v 일 수도 있다. 따라서 모든 간선에 대해 간선 경감을 **|V| - 1** 회 수행한다. (V : 정점 개수)

![Bellman%20Ford/Untitled%202.png](Bellman%20Ford/Untitled%202.png)

1. **초기화 작업** : 시작 정점을 제외한 모든 정점을 INF 값으로 초기화한다. 

![Bellman%20Ford/Untitled%203.png](Bellman%20Ford/Untitled%203.png)

2.  **간선 경감 수행** : 정점 개수 - 1 만큼 간선 경감을 수행한다.

![Bellman%20Ford/Untitled%204.png](Bellman%20Ford/Untitled%204.png)

3.  **사이클 검사**: 음의 가중치가 포함된 사이클이 있는 지 검사한다. 존재하면 false, 존재하지 않으면 true를 반환

> 벨만-포드는 음수 가중치가 사이클을 이루고 있으면 적용할 수 없다. 사이클을 돌면 돌수록 거리 가중치 값이 작아져 최단 경로를 구하는 의미가 없어지기 때문이다. 따라서 2번 과정 수행 후 마지막으로 그래프 모든 엣지에 대해 간선 경감을 1번 수행하여 값이 업데이트하는 지 확인해야한다. 업데이트가 된다면 음수 가중치 사이클이 존재한다는 의미로 결과를 구할 수 없다는 false를 반환하면 된다.

**시간복잡도**

- 시작 정점을 제외한 나머지 정점의 개수(|V| - 1)만큼 간선 경감을 반복한 후 음수 사이클 검사를 위해 그래프의 모든 간선에 대한 탐석을 1번 더 수행한다. O(|V|*|E|)
- 최악(Dense Graph)의 경우 다익스트라는 O(|V|²)의 시간 복잡도를 가지며 벨만-포드는 O(|V|³)의 시간 복잡도를 가진다.

### 백준 문제 풀이

11657 타임머신

    #include <iostream>
    #include <vector>
    #define INF 2e9
    using namespace std;
    vector<pair<int, int> > w[501];
    int d[501];
    int n, m;
    
    bool solve() {
    	bool cycle = false;
    	for (int i=1; i<=n+1; i++) {
    		for (int j=1; j<n+1; j++) {
    			for (int k=0; k<w[j].size(); k++) {
    				if (d[j] == INF) continue;
    				if (d[j] + w[j][k].first < d[w[j][k].second]) {
    					d[w[j][k].second] = d[j] + w[j][k].first;
    					if (i == n+1) {
    						return true;
    					}
    				}
    			}
    		}
    	}
    	return cycle;
    }
    int main() {
    	int a, b, c;
    	cin >> n >> m;
    	
    	fill(&d[1], &d[501], INF);
    	d[1] = 0;
    	for (int i=0; i<m; i++) {
    		cin >> a >> b >> c;
    		w[a].push_back({c, b});
    	}
    	if (solve()) {
    		cout << "-1";
    	} else {
    		for (int i=2; i<n+1; i++) {
    			if (d[i] == INF) cout << "-1" << "\n";
    			else cout << d[i] << "\n";
    		}
    	}
    	
    	return 0;
    }

**참고**

[벨만-포드 알고리즘](https://ratsgo.github.io/data%20structure&algorithm/2017/11/27/bellmanford/)