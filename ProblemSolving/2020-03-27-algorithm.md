# 백준 문제 - 문자열 처리

- Title : [백준] 문자열 처리
- Date : 2020-03-27
- Category : 알고리즘 문제 풀기

# Why?

C++ 로 문자열 문제를 풀 때 입출력, 파싱 등 부분에서 막힌다는 생각이 들어서 연습겸 문자열 관련 문제만 풀어보았다. 난이도는 브론즈 정도?

## 11654 아스키 코드

알파벳 소문자, 대문자, 숫자 0-9중 하나가 주어졌을 때, 주어진 글자의 아스키 코드값을 출력하세요.

    #include <iostream>
    using namespace std;
    
    int main() {
    	char c;
    	cin >> c;
    	cout << (int) c;
    	return 0;
    }

> 'A' 는 65, 'a' 는 97

## 1152 단어의 개수

영어 대소문자와 띄어쓰기만으로 이루어진 문자열이 주어진다. 이 문자열에는 몇 개의 단어가 있을까? 이를 구하는 프로그램을 작성하시오. 단, 한 단어가 여러 번 등장하면 등장한 횟수만큼 모두 세어야 한다.

    #include <iostream>
    using namespace std;
    
    int main() {
    	string str;
    	int ans = 0;
    	getline(cin, str);
    	cin.ignore();
    	
    	for (int i=0; i<str.size(); i++) {
    		if (str[i] == ' ') ans++;
    	}
    	
    	if (str[str.size()-1] == ' ') ans--;
    	if (str[0] != ' ') ans++;
    	
    	cout << ans;
    	return 0;
    }

> 문자열을 공백으로 파싱해야할 때

    #include <iostream>
    #include <vector>
    #include <sstream>
    using namespace std;
    
    int main() {
    	string str;
    	
    	getline(cin, str);
    	cin.ignore();
    	
    	string buf;
    	stringstream ss(str);
    	vector<string> tokens;
    	
    	while (ss >> buf) {
    		tokens.push_back(buf);
    	}
    	
    	cout << tokens.size();	
    	
    	return 0;
    }

> `getline(cin, str)` 로 공백을 포함한 문자열을 입력받을 수 있다. 단, `\n` 을 포함하지않으므로 버퍼에서 `\n` 을 제거하기 위해 `cin.ignore()` 을 해야한다.

## 10809 알파벳 찾기

알파벳 소문자로만 이루어진 단어 S가 주어진다. 각각의 알파벳에 대해서, 단어에 포함되어 있는 경우에는 처음 등장하는 위치를, 포함되어 있지 않은 경우에는 -1을 출력하는 프로그램을 작성하시오.

    #include <iostream>
    using namespace std;
    
    int main() {
    	string str;
    	cin >> str;
    	int ans[26];
    	fill(&ans[0], &ans[26], -1);
    	
    	for (int i=str.size()-1; i>=0; i--) {
    		int c = (int) str[i];
    		ans[c - 97] = i;
    	}
    	
    	for (int i=0; i<26; i++) {
    		cout << ans[i] << ' ';
    	}
    	
    	return 0;
    }

## 1157 단어공부

알파벳 대소문자로 된 단어가 주어지면, 이 단어에서 가장 많이 사용된 알파벳이 무엇인지 알아내는 프로그램을 작성하시오. 단, 대문자와 소문자를 구분하지 않는다.

    #include <iostream>
    using namespace std;
    
    int main() {
    	char str[1000000];
    	int cnt[26] = {0};
    	int idx = 0, max = 0, maxi = 0;
    	
    	scanf("%s", str);
    	
    	for (int i=0; str[i]; i++) {
    		if (str[i] < 97) idx = str[i] - 65;
    		else idx = str[i] - 97;
    		
    		cnt[idx]++;
    		if (cnt[idx] > max) {
    			max = cnt[idx];
    			maxi = idx;
    		} else if (cnt[idx] == max) {
    			maxi = -1;
    		}
    	}
    	
    	printf("%c", maxi == -1 ? '?' : maxi + 65);
    	
    	return 0;
    }

## 2908 상수

상수는 수를 다른 사람과 다르게 거꾸로 읽는다. 예를 들어, 734와 893을 칠판에 적었다면, 상수는 이 수를 437과 398로 읽는다. 따라서, 상수는 두 수중 큰 수인 437을 큰 수라고 말할 것이다. 두 수가 주어졌을 때, 상수의 대답을 출력하는 프로그램을 작성하시오.

### strcmp로 문자열 비교

    #include <iostream>
    #include <string.h>
    using namespace std;
    
    int main() {
    	char a[4], b[4];
    	scanf("%s %s", a, b);
    	
    	swap(a[2], a[0]);
    	swap(b[2], b[0]);
    	
    	if (strcmp(a, b) < 0) printf("%s", b);
    	else printf("%s", a);
    	
    	return 0;
    }

### atoi로 char * → int로 변경 후 비교

    #include <iostream>
    #include <string>
    using namespace std;
    
    int main() {
    	char a[4], b[4];
    	scanf("%s %s", a, b);
    	
    	swap(a[2], a[0]);
    	swap(b[2], b[0]);
    	
    	int na = atoi(a);
    	int nb = atoi(b);
    	
    	if (na > nb) printf("%d", na);
    	else printf("%d", nb);
    	
    	return 0;
    }

- a, b 값을.. 3으로 했을 때 입력을 제대로 못 받는 문제 생김 ㅜ_ㅜ
    - c의 문자열은 `\0` 을 기준으로 구분한다. 문자열의 끝에 있는 `\0` 이 어떤 식으로든 없어지게 된다면 그 뒤에 `\0` 이 나타날 때까지 진행하게 된다
    - 123 456 일 때, a, b 크기를 3으로 하면 a에는 "123"이 들어간다. 그럼 `\0` 은 b[0]에 들어갔을 거다. 이 상태로 456을 입력받으면 b[0]에 4가 쓰여진다. 마찬가지로 b의  `\0` 도 메모리 어딘가에 들어가게된다.
    - 즉, a 출력시 123456이 나오는 이유는 123을 입력받을 때 `\0` 을 받지 못해 456까지 입력받게 되서다.