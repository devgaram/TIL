# Pattern Searching - Naive algorithm

- 알고리즘
- 2020년 03월 10일

## Naive algorithm

문자열 인덱스 0부터 패턴 문자열은 하나씩 비교해가는 방법. 가장 직관적이면서 순수(?)하다.
```cpp
    #include <iostream>
    #include <cstring>
    using namespace std;
    
    void search(char* pat, char* txt) {
    	int M = strlen(pat);
    	int N = strlen(txt);
    	
    	for (int i=0; i<=N-M; i++) {
    		int j;
    		for (j=0; j<M; j++) {
    			if (txt[i + j] != pat[j]) break;
    		}
    		if (j == M)
    			cout << "Pattern found at index " << i << endl;
    	}
    }
    int main() {
    	char txt[] = "AABAACAADAABAABA";
    	char pat[] = "AABA";
    	search(pat, txt);
    	return 0;
    }
```
## 시간 복잡도

### 최선의 시간 복잡도

- txt[] = "AABCCAADDEE"
- pat[] = "FAA"

패턴의 첫번째 문자 'F'가 일치하는 경우가 없어서 `O(n)`의 시간 복잡도를 가진다. n: 탐색 문자열의 길이

### 최악의 시간 복잡도

- txt[] = "AAAAAAAAAAAAAAAAAA"
- pat[] = "AAAAB"

패턴의 마지막 문자 'B'만 다를 경우 `O(m*(n-m+1))`의 시간 복잡도를 가진다. m: 패턴 문자열의 길이, n: 탐색 문자열의 길이