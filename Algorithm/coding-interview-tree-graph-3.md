---
title: 알고리즘 개념 잡자 4탄 - 트라이
date: 2020-01-03
tags: [coding-interview, data-structure]
category: algorithm
---

> 참고 서적<br/>
> [코딩 인터뷰 완전분석](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=115116545) 트리와 그래프<br/>
> [C언어로 쉽게 풀어쓴 자료구조](http://www.yes24.com/Product/Goods/69750539) 제 7장 트리, 제 8장 우선순위 큐

# 3. 트라이(접두사 트리, prefix tree)
> 참조 링크 [wikipedia](https://en.wikipedia.org/wiki/Trie)

![트라이](/assets/images/2020-01-03-img/4.png)

- n-차 트리의 변종으로 각 노드에 문자를 저장하는 자료구조이다.
- 문자열 탐색에 효율적인 자료구조이다.
- 트리를 아래쪽으로 순회하면 단어 하나가 나온다.
- TerminatingTrieNode로 널 노드를 표현한 경우, 트라이에서 각 노드는 1개 ~ 문자열 길이 + 1개 까지 자식을 가질 수 있다.
- 널 노드 대신 불린 플래그로 표현한 경우, 각 노드는 0개 ~ 문자열 길이개까지 자식을 가질 수 있다.

> **널 노드(* 노드)**
> - 단어의 끝을 나타낸다.
> - 예) MANY 이후에 널 노드가 나오면 MANY라는 단어가 완성되었다는 의미다.
>
> **널 노드 구현 방법**
> - TrieNode를 상속한 TerminatingTrieNode로 표현하기
> - 널 노드의 부모 노드 안에 불린 플래그를 새로 정의함으로써 표현하기

**어디에 사용할까?**
- 자동 완성
- 문자열이 어떤 문자열의 접두사인지 확인하고 싶을 때 <span class="clr-grey">cf) 단순 삽입/조회만 한다면 해시테이블 사용을 권장</span>
- 예: 'a', 'axe'로 시작하는 단어를 찾고 싶을 때

> 예시, M, MA, MAN, MANY를 차례대로 살펴보는 경우 트리의 현재 노드를 참조값으로 넘김으로써 루트 노드에서 시작할 필요가 없고 단순히 Y가 MAN의 자식인지만 확인해보면 된다.

## 트라이 VS 해시테이블

- 최악의 경우, 시간복잡도는?
  - 트라이 : O(K) (K: 가장 긴 문자열)
  - 해시테이블 : 충돌이 자주 발생되었다면 O(N)의 시간복잡도를 가진다. (N: 키의 개수) 또한 입력 문자열을 기반으로 해시 계산을 하므로 O(K)의 시간이 걸린다.
  -  cf) 탐색할 문자열이 트라이에 없는 경우에는 문자열 길이 K보다 시간이 덜 걸린다.

> **트라이와 해시의 시간복잡도가 비슷하다..?** <br/>
> 길이가 K인 무자열이 주어졌을 때 트라이는 O(K) 시간에 해당 문자열이 유효한 접두사인지 확인할 수 있다. 이 시간은 해시테이블 사용했을 때와 정확히 같은 수행 시간이다. 우리가 종종 해시테이블을 검색하는 시간이 O(1)이라고 하지만 완전히 맞는 말은 아니다. 해시테이블도 입력 문자열은 전부 읽어야 하므로 길이가 K인 단어를 검색하는 데 걸리는 시간은 O(K)가 된다.

- 알파벳 정렬?
  - 일반적으로 자료구조에 문자열을 저장한다면 각 문자열의 순서가 중요하기도 하다. 트라이는 노드를 저장할 때 왼쪽에서 오른쪽으로 사전 순으로 저장한다면 쉽게 정렬이 가능하다.

- 기타
  - 트라이는 삭제가 직관적이며 해시 함수가 필요 없으나 해시 테이블 보다는 많은 공간을 차지한다.

## 트라이 구현

```javascript
class Trie {
  constructor(key) {
    this.is_terminal = false
    this.key = key
    this.trie_child = Array(26).fill(null)    
  }

  char_to_index(key) {
    return key.charCodeAt(0) - 'a'.charCodeAt(0)
  }

  // 새로운 문자열을 트라이에 추가
  insert (key) {
    let current_trie = this
    for (let i=0; i<key.length; i++) {      
      let index = this.char_to_index(key[i])
      if (current_trie.trie_child[index] === null) current_trie.trie_child[index] = new Trie(key.slice(0, i+1))
      current_trie = current_trie.trie_child[index]   
    }
    current_trie.is_terminal = true
  }

  // 트라이 출력
  print (trie, dep) {
    console.log(dep, trie.key)
    if (trie.is_terminal) return
    for (let i=0; i<26; i++) {
      if (trie.trie_child[i] !== null) this.print(trie.trie_child[i], dep + 1)
    }
  }

  // key를 접두어로 가지고 있는지, 가지고 있으면 해당 접두어가 끝나는 부분의 위치를 반환
  find (key) {
    let current_trie = this
    for (let i=0; i<key.length; i++) {      
      let index = this.char_to_index(key[i])
      if (current_trie.trie_child[index] === null) return false
      else current_trie = current_trie.trie_child[index]   
    }
    return current_trie
  }

  // key를 포함하는 지, 포함하면 true
  exist (key) {
    let current_trie = this
    for (let i=0; i<key.length; i++) {      
      let index = this.char_to_index(key[i])
      if (current_trie.trie_child[index] === null) return false
      else current_trie = current_trie.trie_child[index]   
    }
    return current_trie.is_terminal
  }
  

}

var trie = new Trie(null)
var arr = ['to','tea', 'ted', 'ten', 'a', 'inn']
arr.forEach(str => trie.insert(str))
// trie.print(trie, 0)
console.log(trie.exist('tea')) // true
console.log(trie.exist('aaa')) // false
console.log(trie.exist('a')) // true
console.log(trie.find('te')) // Trie {is_terminal: false, key: "te", trie_child: Array(26)}

```