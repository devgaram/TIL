# c++로 알고리즘 풀 때 팁들

- Title : c++로 알고리즘 풀 때 팁들
- Date : 2020-01-28
- Category: Algorithm

# # 입출력

cin, cout은 느려서 scanf, printf를 사용하는 걸 추천한다.

```cpp
int N, M, K;
scanf("%d %d %d", &N, &M, &K);
```

# # 2차원 배열

1 2 3 4 5 <br/>
2 3 4 5 6 <br/>
... 방식의 입력을 받고 싶다면?

```cpp
#define MAX 10
int A[MAX][MAX];

int main() {

  for (int i=0; i<N; i++) {
    for (int j=0; j<N; j++) {
      scanf("%d", &A[i][j]);
    }
  }
  return 0;
}
```

## 2차원 deque 입력, 정렬

```cpp
#define MAX 10
deque<int> T[MAX][MAX];
// ...
int main() {
  int x, y, age;

  // 입력
  for (int i=0; i<M; i++) {
    scanf("%d %d %d", &x, &y, &age);
    T[--x][--y].push_back(age);
  }

  // 정렬 오름차순
  for (int i=0; i<N; i++) {
    for (int j=0; j<N; j++) {
      if (T[i][j].size() > 1)
        sort(T[i][j].begin(), T[i][j].end());

    }
  }

  // 순회
  for (int i=0; i<N; i++) {
    for (int j=0; j<N; j++) {
      deque<int> &tmp = T[i][j]; // 참조로 받기(수정하려면)
      for(auto iter=tmp.begin(); iter != tmp.end();) {
        int &t = *iter;
        if () //삭제X
          iter++;
        else //삭제 O
          iter = tmp.erase(iter); //다음 iter 받음

      }
    }
  }
  return 0;
}
```

# 포인터와 참조

```cpp
// 새로운게임2 에서..
struct Horse {
  int row, col, dir;
};

// 참조 변수 : 대상을 직접 할당
// 선언과 동시에 초기화해야한다!
// NULL 불가
Horse &h = horse[0];
h = horse[1]; // 에러!! (대상 변경 불가)
printf("%d", h.row);

// 포인터 변수 : 주소값 할당
Horse *h = &horse[0];
h = &horse[1];  // 대상 변경 가능!
Horse *h = NULL; // NULL 가능

if (h) printf("%d", h->row);
// 또는
if (h) printf("%d", (*h).row);
```

# 배열 초기화할 때, memset 함수 쓰자!

- for문보다 더 빠른 속도가 나올 수 있다.
- 특정 범위에 있는 **연속된 메모리**에 값을 지정하고 싶을 때 쓰자!
- cstring 헤더

```cpp
void* memset(void *ptr, int value, size_t num);
/*
ptr : 메모리 시작 포인터(주소) => 배열 이름
value: 채울 값 => 값
num: 채우고자 하는 바이트 수(메모리 크기) => sizeof(배열이름)
*/
```

```cpp
#include <cstring>
using namespace std;
int main() {
  int C[21][21];
  memset(C, 0, sizeof(C));
  return 0;
}
```

# 구조체 멤버 내용 복사하고 싶으면? memcpy를 쓰자

```cpp
#include <cstring>
using namespace std;

struct Horse {
  int row, col, dir;
};

int main() {
  Horse h1, h2;
  h1.row = 1;
  h1.col = 2;
  h1.dir = 3

  memcpy(&h2, &h1, sizeof(Horse)); // Horse 구조체 크기만큼 h1 내용을 h2에 복사
  return 0;
}
```

## 동적 할당된 경우는?

```cpp
#include <cstring>
using namespace std;

struct Horse {
  int row, col, dir;
};

int main() {
  Horse *h1 = malloc(sizeof(Horse));
  Horse *h2= malloc(sizeof(Horse));
  h1->row = 1;
  h1->col = 2;
  h1->dir = 3

  memcpy(h2, h1, sizeof(Horse)); // Horse 구조체 크기만큼 h1 내용을 h2에 복사
  return 0;
}
```

## 또 다른 예

```cpp
Horse h1;
Horse *h2= malloc(sizeof(Horse));

memcpy(h2, &h1, sizeof(Horse));    // 구조체 변수에서 동적 메모리로 복사
```

# 자료형

정수 자료형 int는 4바이트 정보를 기록할 수 있는 자료형으로, signed int(부호가 있는 정수)를 기준으로 기록할 수 있는 양의 정수 범위는 0 ~ 2,147,483,647 (16진수로 7FFFFFFF)이다.

## int 범위를 벗어나는 경우 자료형은 무엇을 쓸까?

8바이트 크기를 가지는 정수 자료형 long long을 쓰는 게 좋다. signed long long을 기준으로 하면 최대 계산할 수 있는 양의 정수 범위는 0 ~ 9,223,372,036,854,775,807(16진수로 7FFFFFFFFFFFFFFF)다.

long long으로도 불가능하다면 BigInteger를 사용하면 된다.

```cpp
typedef long long ll;
ll large, small;

```

# 순열관련 함수

prev_permutation
