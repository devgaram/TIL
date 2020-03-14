# Pattern Searching - KMP Algorithm

- 분류
- 2020년 03월 11일

## KMP

kmp 알고리즘은 Pattern Searching - Naive Alogorithm(TIL 알고리즘 파트에서 내용을 볼 수 있음)과 비슷하나 0~N(탐색문자길이) 탐색 중 적절하게 문자를 건너뛴다. 아래 예시로 이해해보자.

- txt[] = ABABCAAA
- pat[] = ABABD

첫번째 윈도우 탐색 결과 패턴을 찾지는 못했다.

- txt[] = `ABABC`AAA
- pat[] = `ABABD`

Naive 알고리즘 방식대로 이어서 두번째 윈도우를 탐색해보자.

- txt[] = A`BABCA`AA
- pat[] = `ABABD`

Naive 알고리즘 방식대로 이어서 세번째 윈도우를 탐색해보자.

- txt[] = AB`ABCAA`A
- pat[] = `ABABD`

하지만 KMP 알고리즘은 적절하게 문자를 건너뛰어서 첫번째 윈도우 후 다음으로 탐색할 두번째 윈도우는 아래와 같다.

- txt[] = AB`ABCAA`A
- pat[] = `ABABD`

첫번째 윈도우 탐색 결과 ABAB까지는 패턴과 일치하는 것을 이용한 것이다. 패턴의 부분 문자열이기도한 ABAB는 접두사 AB와 접미사 AB가 일치한다. 다음 패턴의 시작점을 접미사의 시작점으로 할 수가 있는 거다.

## lps[] 란?

- lps 배열의 크기는 패턴의 길이와 같고 패턴 탐색 시 몇 개의 문자를 건너뛸지를 결정한다.
- lps[i]는 패턴의 0~ i 까지의 부분 문자열에서의 접두어(prefix)와 접미어(suffix)가 같은 부분 문자열 중 가장 긴 것의 길이다. 단 접두어로 전체 문자열은 허용하지않는다.

## Ips[] 예시

"AAAA"

- "A" : Ips[0] = **0** (prefix는 전체 문자열이 안됨)
- "AA" : lps[1] = **1** ( prefix `A` suffix `A` )
- "AAA" : lps[2] = **2** ( prefix `AA` suffix `AA` )
- "AAAA" : lps[3] = **3** ( prefix `AAA` suffix `AAA` )

"ABCDE"

- "A" : Ips[0] = **0**
- "AB" : lps[1] = **0**
- "ABC" : lps[2] = **0**
- "ABCD" : lps[3] = **0**
- "ABCDE" : lps[4] = **0**

"AABAACAABAA"

- "A" : lps[0] = 0
- "`A` `A`" : lps[1] = 1
- "AAB" : lps[2] = 0
- "`A`AB`A`" : lps[3] = 1
- "`AA`B`AA`" : lps[4] = 2
- "AABAAC" : lps[5] = 0
- "`A`ABAAC`A`" : lps[6] = 1
- "`AA`BAAC`AA`" : lps[7] = 2
- "`AAB`AAC`AAB`" : lps[8] = 3
- "`AABA`AC`AABA`" : lps[9] = 4
- "`AABAA`C`AABAA`" : lps[10] = 5

## lps를 이용해 탐색하기

- 현재 txt 윈도우에서 pat[j] j는 0부터 비교를 시작하기
- txt[i]와 pat[j]가 일치하면 i와 j를 증가하기
- txt[i]와 pat[j]가 일치하지않으면
    - 패턴 0~j-1과 탐색문자 i-j~i-1 까지는 일치한다는 것은 알고 있다.
    - lps[j-1]은 패턴의 0~j-1 부분 문자열의 접두어 접미어가 일치하는 부분 문자열 중 길이가 가장 긴 값이라는 것은 위 정의를 통해 이미 알고 있다.
    - 현재 윈도우 txt[i-j~i-1]에서 lps[j-1]만큼은 탐색할 필요는 없다.

## C++

"AABAACAABAA"

- lps[0] = 0
- i = 1, len = 0 ⇒ **lps[1] = 1**
    - pat[1] == pat[0] , len=1, lps[1] = 1, i = 2
- i = 2, len = 1
    - pat[2] != pat[1] && len ! = 0, len = lps[1-1] =lps[0] = 0
- i = 2, len = 0 ⇒ **lps[2] = 0**
    - pat[2] ≠ pat[0] && len == 0, lps[2] = 0, i = 3
```cpp
    #include <iostream>
    #include <cstring>
    using namespace std;
    
    void computeLPSArray(char* pat, int M, int *lps) {
    	// 현재까지 계산한 lps 중 가장 큰 값
    	int len = 0;
    	// 부분문자열 길이가 1이면 lps 값은 언제나 0
    	lps[0] = 0;
    	
    	// i ~ M - 1 까지 계산
    	int i = 1;
    	while (i < M) {
    		if (pat[i] == pat[len]) {
    			len++;
    			lps[i] = len;
    			i++;
    		} else {
    			if (len != 0) {
    				len = lps[len - 1];
    			} else {
    				lps[i] = 0;
    				i++;
    			}
    		}
    	}
    }
    void KMPSearch(char* pat, char* txt) {
    	int M = strlen(pat);
    	int N = strlen(txt);
    	
    	int lps[M];
    	
    	computeLPSArray(pat, M, lps);
    	
    	int i = 0, j = 0;
    	while (i < N) {
    		if (pat[j] == txt[i]) {
    			j++;
    			i++;
    		}
    		if (j == M) {
    			cout << "Found Pattern at index " << (i - j) << endl;
    			j = lps[j - 1];
    		} 
    		// 패턴이 일치하지않으면
    		else if (i < N && pat[j] != txt[i]) {
    			// 미스매칭이 1~M이면
    			if (j != 0)
    				j = lps[j - 1];
    			else // 첫 문자부터 미스매칭
    				i = i + 1;
    		}
    	}
    }
    int main() {
    	char txt[] = "AABAACAADAABAABA";
    	char pat[] = "AABA";
    	KMPSearch(pat, txt);
    	return 0;
    }
 ```
