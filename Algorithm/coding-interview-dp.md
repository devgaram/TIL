---
title: 알고리즘 개념 잡자 5탄 - 재귀와 동적 프로그래밍
date: 2020-01-09
tags: [coding-interview, data-structure]
category: [algorithm]
---

> 참고 <br/>
> - [코딩 인터뷰 완전분석](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=115116545) 재귀와 동적 프로그래밍
> - [Geeksforgeeks 바로가기](https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/)

# 동적 프로그래밍
- 주어진 문제를 부분 문제로 나눠 값을 계산하는 것
- 중복되는 계산을 막기 위해 부분 문제의 결과 값을 저장함 - 메모이제이션(Memoization)과 타뷸레이션(Tabulation)

## 메모이제이션(Memoization)과 타뷸레이션(Tabulation)
- 메모이제이션 : 하향식 접근법 (Top Down) - 재귀적
  - 테이블을 만들고 NIL(예, -1) 값을 넣음
  - 서브 문제의 결과값을 구할 때, 먼저 테이블 검색
  - 계산된 값이 있으면 얘를 리턴
  - NIL이면 계산 실행하여 계산 결과를 테이블에 저장
  - 요구가 있을 때 채워지므로 테이블에 데이터가 다 채워지지 않을 수도 있음
- 타뷸레이션 : 상향식 접근법 (Bottom Up) - 순환적
  - 테이블의 마지막 요소를 반환하는 구조
  - 첫번째부터 전체가 채워지는


## DP의 2가지 속성
모든 DP 문제는 Overlapping subproblems 속성을 만족하고 대부분의 클래식 DP 문제는 Optimal Substructure 속성을 만족한다.

**1. overlapping subproblems** <br/><br/>
- 부분 문제들이 공통으로 겹치는 부분이 있는 지?<br/>
> <span class="clr-grey">우리가 메모이제이션이나 타뷸레이션을 사용하는 이유는 부분 문제의 결과값을 저장해놔서 나중에 이를 활용하려고 사용하죠. 근데 겹치는 부분 문제가 없으면?? 이게 필요가 없음.</span>

- 동적 프로그래밍과 분할 정복      
  - 문제를 나누는 방식에 차이가 있음 <br/><br/>
  - 피보나치<br/>
  ![피보나치](/assets/images/2020-01-10-img/2.png)
  - 이진탐색 <br/>
  ![이진탐색](/assets/images/2020-01-10-img/1.png)

<br/>

**2. Optimal Substructure** <br/>
- 문제의 답을 부분 문제의 답에서 구할 수 있는 가?
- 최단 경로 vs 최장 경로<br/>
  - 경로안에 무수히 많은 경로가 있을 때, 중간 정점들이 각각 최단이 된다면 이를 모든 이은 경로 또한 최단이 된다. <br/><br/>
  ![경로](/assets/images/2020-01-10-img/3.gif)
    - q -> t 최단 : q -> r + r -> t
    - q -> t 최장 : q -> s -> t -> r + r -> q -> s -> t

## 문제 푸는 방법

**1. DP 문제인가?**
- DP의 2가지 속성으로 판단하기

**2. 상태 찾기**
- 서브 문제마다 값이 달라지는 변수 찾기
- 각 서브 문제를 구별하는 값
- 파라미타들
- 적을 수록 좋다.
- DP(상태) = 결과값(예, 최고 이익, 경우의 수..)

**3. 재귀 방법 알아내기 == 상태들의 관계 알아내기** <br/>
- state(n) = state(n-1) + state(n-3) + state(n-5)

**4. 메모이제이션과 타뷸레이션 쓰기**

# 8.1 트리플 스텝
어떤 아이가 n개의 계단을 오른다. 한 번에 1계단 오르기도 하고 2계단이나 3계단을 오르기도 한다. 계단을 오르는 방법이 몇 가지가 있는지 계산하는 메서드를 구현하라.

- Step 1 : DP 문제인지 판별하기
  - 느낌..
- Step 2 : 부분 문제마다 값이 달라지는 변수 찾기
  - N : 계단의 개수 => 매개변수
  - DP[N] = N을 만족하는 경우의 수
- Step 3 : 재귀 방법을 알아내기
  - 조정할 수 있는 거, 오를 수 있는 계단 개수져
  - upStair(n) = (n - 1) || (n - 2) || (n - 3)
  - upStair(n) = (n - 1) + (n - 2) + (n - 3)
- Step 4 : 메모이제이션, 타뷸레이션

```cpp
#include <iostream>
#include <vector>

using namespace std;

int solution(int n, vector<int>& memo) {
  if (n < 0) return 0;
  if (n == 0) return 1;
  if (memo[n] > -1) return memo[n];
  memo[n] = solution(n - 1, memo) + solution (n - 2, memo) + solution (n - 3, memo);
  return memo[n];
}

int solution_BU(int n) {
  vector<int> dp(n+1, -1);
  dp[0] = 1;
  dp[1] = 1;
  dp[2] = 2;
  for (int i=3; i<=n; i++) {
    dp[i] = dp[i-1] + dp[i-2] + dp[i-3];
  }
  return dp[n];
}

int main(){
  int n = 10;
  vector<int> memo(n+1, -1);
  std::cout << solution(n, memo) << ",";
  std::cout << solution_BU(n) << std::endl;
  return 0;
}
```

# 8.11 코인
쿼터(25센트), 다임(10센트), 니켈(5센트), 페니(1센트)의 네 가지 동전이 무한히 주어졌을 때, n센트를 표현하는 모든 방법의 수를 계산하는 코드를 작성하라

- Step 1 : DP 문제인지 판별하기
  - 10원 방법의 수는 5원 방법에 + 5를 더하는 것....
  - 느낌..
- Step 2 : 부분 문제마다 값이 달라지는 변수 찾기
  - 부분 문제는 1원일 때 방법의 수... 2원일 때 방법의 수..3원일 때....N원일 때 방법의 수
  - N : 구할 센트, => 매개변수, 상태값
  - D[N] = 방법의 수
- Step 3 : 재귀 방법을 알아내기**
  - 재귀의 방법을 조정할 수 있는 값은? 동전의 가치 (25, 10, 5, 1) 
  - D(n) = D(n - 25) + D(n - 10) + D(n - 5) + D(n - 1)
- Step 4 : 메모이제이션, 타뷸레이션

- 10센트 만들기 (D[0] = 1)
  - 25센트로 : 0 0 0 0 0 0 0 0 0 0
  - 10센트로 : 0 0 0 0 0 0 0 0 0 1
  - 5센트로  : 0 0 0 0 1 0 0 0 0 2
  - 1센트로  : 1 1 1 1 2 2 2 2 2 4 

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int coin[4] = {25, 10, 5, 1};
int solution(int n, int index, vector<vector<int> >& memo) {
  if (index >= 3) return 1;
  if (memo[n][index] > 0) return memo[n][index];
  int answer = 0;
  for (int i=0; i * coin[index] <= n; i++) {
    answer += solution(n - i * coin[index], index + 1, memo);
  }
  
  memo[n][index] = answer;
  return memo[n][index];
}

int solution_BU(int n) {
  vector<int> dp(n+1, 0);
  dp[0] = 1;
  for (int i=0; i<4; i++) {
    for (int j=1; j<=n; j++) {
      if (j - coin[i] >= 0) dp[j] += dp[j-coin[i]]; 
    }
  }
  return dp[n];
}

int main() {
  int n = 10;
  vector<vector<int> > memo(n + 1, vector<int>(4, 0));
  int answer = solution(n, 0, memo);
  std::cout << answer << std::endl;
  int answer_2 = solution_BU(n);
  std::cout << answer << std::endl;
  return 0;
}
```

# 8.7 중복 없는 순열
문자열이 주어졌을 때 모든 경우의 순열을 계산하는 메서드를 작성하라. 단, 문자는 중복되어 나타날 수 없다.


# 8.9 괄호
n-쌍의 괄호로 만들 수 있는 모든 합당한(괄호가 적절히 열리고 닫힌) 조합을 출력하는 알고리즘을 구현하라

```cpp
#include <string>
#include <vector>
#include <iostream>

using namespace std;
void fnc(string result, int cur, int dep, int n, vector<string>& answer) {
  if (cur < 0) return;
  if (cur > 0 && n - dep < cur) return;
  if (cur == 0 && dep == n) {
    answer.push_back(result);
    return;
  }
  fnc(result + "(", cur + 1, dep + 1, n, answer);
  fnc(result + ")", cur - 1, dep + 1, n, answer);
}

vector<string> solution(int n) {    
    vector<string> answer;
    fnc("", 0, 0, n*2, answer); 
    return answer;
}

void solution_2(int leftRem, int rightRem, int index, char *str, vector<string>& answer) {
  if (leftRem < 0 || leftRem > rightRem) return;
  if (leftRem == 0 && rightRem == 0) {
    answer.push_back(str);
    return;
  }
  str[index] = '(';
  solution_2(leftRem - 1, rightRem, index+1, str, answer);

  str[index] = ')';
  solution_2(leftRem, rightRem - 1, index + 1, str, answer);
}

int main() {
  int n;
  cout << "input:";
  cin >> n;
  vector<string> answer = solution(n);
  for (int i=0; i<answer.size(); i++) {
    cout << answer[i] << " ";
  }
  cout << endl;
  vector<string> answer2;
  char str[n*2];
  solution_2(n, n, 0, str, answer2);
  for (int i=0; i<answer2.size(); i++) {
    cout << answer2[i] << " ";
  }
  
  return 0;
}

```

# 8.10 영역 칠하기
화면(색이 칠해진 이차원 배열), 화면상의 한 지점, 새로운 색상이 주어졌을 때, 주어진 지점과 색이 같은 주변 영역을 새로운 색상으로 다시 칠하라

- BFS

```cpp
#include <string>
#include <vector>
#include <iostream>
#include <queue>

using namespace std;

int row[4] = {0, 0, -1, 1};
int col[4] = {-1, 1, 0, 0};

int solution(vector<vector<int> >& screen, pair<int, int>& point, int newColor) {
  
  queue<pair<int, int> > q;
  int next_row, next_col;
  int pre_color = screen[point.first][point.second];
  q.push(point);  
  while (!q.empty()) {
    pair<int, int> pop = q.front();
    screen[pop.first][pop.second] = newColor;
    q.pop();
    for (int i=0; i<4; i++) {
      next_row = pop.first + row[i];
      next_col = pop.second + col[i];
      if (next_row < 0 || next_col < 0) continue;
      if (next_row >= screen.size() || next_col >= screen[0].size()) continue;
      if (screen[next_row][next_col] == pre_color) {
        q.push(make_pair(next_row, next_col));
      }
    }
  }
  return 0;
}

int main() {
  // vector<vector<int> > screen({
  //   vector<int>({1, 1, 2, 3}),
  //   vector<int>({2, 1, 2, 3}),
  //   vector<int>({1, 1, 2, 2}),
  //   vector<int>({2, 2, 2, 1})
  // });
  /*
  1 2 2 3
  2 1 2 3
  1 1 2 2
  2 2 2 1
  */
 vector<vector<int> > screen(4, vector<int>(4));
  screen[0][0] = 1;
  screen[0][1] = 2;
  screen[0][2] = 2;
  screen[0][3] = 3;

  screen[1][0] = 2;
  screen[1][1] = 1;
  screen[1][2] = 2;
  screen[1][3] = 3;
  
  screen[2][0] = 1;
  screen[2][1] = 1;
  screen[2][2] = 2;
  screen[2][3] = 2;

  screen[3][0] = 2;
  screen[3][1] = 2;
  screen[3][2] = 2;
  screen[3][3] = 1;

  pair<int, int> point = make_pair(1, 2);
  for (int i=0; i<screen.size(); i++) {
    for (int j=0; j<screen[0].size(); j++)
      std::cout << screen[i][j] << " ";
    std::cout << std::endl;
  }
  solution(screen, point, 0);
  std::cout << std::endl;
  for (int i=0; i<screen.size(); i++) {
    for (int j=0; j<screen[0].size(); j++)
      std::cout << screen[i][j] << " ";
    std::cout << std::endl;
  }
  return 0;
}
```

# 8.14 불린값 계산
0(false), 1(true), &(AND), |(OR), ^(XOR)으로 구성된 불린 표현식과 원하는 계산 결과(불린값)이 주어졌을 때, 표현식에 괄호를 적절하게 추가하여 그 값이 원하는 결과값과 같게 만들 수 있는 모든 경우의 수 출력하기

출력 예시
- countEval("1^0|0|1", false) -> 2개
- countEval("0&0&0&1^1|0", true) -> 10개

- Step 1 : DP 문제인지 판별하기
  - 뭔가.. 적절하게 조합해서 모든 경우 구하는 거라서.. dp같네..?
- Step 2 : 부분 문제마다 값이 달라지는 변수 찾기
  - 음.. 전체 수식을 쪼개니깐 수식이 부분문제마다 달라지넵, 매개변수로 쪼개진 수식을 보내야겠댜~
- Step 3 : 재귀 방법을 알아내기**
  - 괄호를 추가한다라..
  - 1^0|0|1 예시로 보면..
  - 1 ^ (0|0|1) 처럼 괄호를 추가하고 싶다는 건데..
  - 그럼 ^ 같은 표현식을 기준으로 왼쪽 오른쪽 나눠어..
  - D[수식] = D[왼쪽 수식] * D[오른쪽 수식]
  - 서로 연관있으니깐 *곱하기
  - 표현식은 index 1, 3, 5, 7 ..에 위치
- Step 4 : 메모이제이션, 타뷸레이션

1       ^  0|0|1         
1^0     |    0|1           
1^0|0   |   1            

```cpp
#include <string>
#include <vector>
#include <iostream>
#include <map>

using namespace std;

int fnc(std::string exp, bool r, map<string, int>& memo) {  
  if (exp.length() == 1) return exp[0] == (r ? '1' : '0') ? 1 : 0;
  if (memo.count(exp + (r ? "-1" : "-0")) == 1) return memo[exp + (r ? "-1" : "-0")];
  int sum = 0;
  for (int i=1; i<exp.length(); i+=2) {
    char e = exp[i];
    string left = exp.substr(0, i);
    string right = exp.substr(i+1);
    if (e == '^') {
      if (r) {  // true
        sum += fnc(left, r, memo) * fnc(right, r^1, memo);
        sum += fnc(left, r^1, memo) * fnc(right, r, memo);
      } else {
        sum += fnc(left, r, memo) * fnc(right, r, memo);
        sum += fnc(left, r^1, memo) * fnc(right, r^1, memo);
      }
     
    } else if (e == '|') {
      if (r) { // true
        sum += fnc(left, r, memo) * fnc(right, r, memo);
        sum += fnc(left, r, memo) * fnc(right, r^1, memo);
        sum += fnc(left, r^1, memo) * fnc(right, r, memo);
      } else { // false
        sum += fnc(left, r, memo) * fnc(right, r, memo);
      }      
    } else {
      if (r) { // true
        sum += fnc(left, r, memo) * fnc(right, r, memo);
      } else { // false
        sum += fnc(left, r, memo) * fnc(right, r, memo);
        sum += fnc(left, r^1, memo) * fnc(right, r, memo);
        sum += fnc(left, r, memo) * fnc(right, r^1, memo);
      }
      
    }    
  }
  memo.insert(pair<string, int>(exp + (r ? "-1" : "-0") , sum));
  return sum;
}

int countEval(std::string exp, bool result) { 
  map<string, int> map;
  return fnc(exp, result, map);
}

int main() {
  std::cout << countEval("1^0|0|1", false) << std::endl;
  std::cout << countEval("0&0&0&1^1|0", true) << std::endl;
  return 0;
}
```