---
layout: post-content
title: 운영체제 - 프로세스 동기화 2
date: 2020-01-14
tags: [os]
categories: [operating-system]
---
> 1. 생산자-소비자 문제(Producer and Consumer Problem)
> 2. 독자-저자 문제(Readers-Writers Problem)
> 3. 식사하는 철학자 문제(Dining Philosopher Problem)

# 전통적 동기화(Classical Synchronization Problems)

## Producer and Consumer Problem
- 생산자-소비자 문제
  - 생산자가 데이터를 생산하면 소비자는 그것을 소비
  - 예1: 컴파일러(생산자) > 어셈블러(소비자)
  - 예2: 파일서버 > 클라이언트
  - 예3: 웹 서버(html 생산함) -> 웹 클라이언트(브라우저로 소비)
- 유한버퍼 문제 (Bounded Buffer Problem)
  - 생산된 데이터는 버퍼에 일단 저장(속도 차이 등)
  - 현실 시스템에서 버퍼 크기는 유한
  - 생산자는 버퍼가 가득 차면 더 넣을 수 없다.
  - 소비자는 버퍼가 비면 뺄 수 없다.

> 예시<br/>
일반적으로 농작물 생산 속도와 소비 속도가 다르다. 현실적으로 바로 농산물을 소비자에게 주는 게 아니라 창고에 우선 저장한다. 저장된 여러 농작물을 소비자가 조금씩 빼먹고 생산자는 계속 생산해서 저장한다. 이 창고가 바로 **버퍼**다. 버퍼는 데이터를 저장할 수 있는 메모리 또는 디스크 공간으로 주로 메모리를 이용하긴 한다. 그런데 버퍼의 크기는 얼마나 될까? 현실 시스템에서 당연히 버퍼 크기는 유한하다. 컴퓨터에서 메모리가 한정적이니 당연한 얘기다.

```java
/****** 버퍼 ******/

class Buffer {
  int[] buf;
  int size;
  int count;  // 버퍼의 현재 자원 크기
  int in; // 생산자 insert 할 버퍼의 위치
  int out;  // 소비자가 remove 할 버퍼의 위치

  Buffer(int size) {
    buf = new int[size];
    this.size = size;
    count = in = out = 0;
  }
  // 생산자
  void insert(int item) {
    /* check if buf is full */
    while (count == size)
    ;
    
    buf[in] = item;
    in = (in+1)%size; //circular
    count++; 
  }
  // 소비자
  int remove() {
    /* check if buf is empty */
    while (count == 0)
    ;
    
    int item = buf[out];
    out = (out+1)%size;
    count--;
    return item;    
  }
}
```

```java
/****** 생산자 ******/
class Producer extends Thread {
  Buffer b;
  int N;
  Producer(Buffer b, int N) {
    this.b = b; this.N = N;
  }
  public void run() {
    for (int i=0; i<N; i++)
    b.insert(i);
  }
}
```

```java
/****** 소비자 ******/
class Consumer extends Thread {
  Buffer b;
  int N;
  Consumer(Buffer b, int N) {
    this.b = b; this.N = N;
  }
  public void run() {
    int item;
    for (int i=0; i<N; i++)
    item = b.remove();
  }
}
```

```java
/****** 메인 ******/
class Test {
  public static void main(String[] arg) {
    Buffer b = new Buffer(100);
    Producer p = new Producer(b, 10000);
    Consumer c = new Consumer(b, 10000);
    p.start();
    c.start();
    try {
      p.join();
      c.join();
    } catch (InterruptedException e) {}
      System.out.println("Number of items in the buf is " + b.count);
  }
}
```

- 위 코드는 잘못된 결과가 나온다.
  - <code>실행 불가</code> 또는 <code>count != 0</code> (생산된 항목 숫자 != 소비자 항목 숫자)
  - 최종적으로 버퍼 내에는 0개의 항목이 있어야 한다.
- 원인은 무엇일까?
  - 공통변수 count, buf[]에 대한 동시 업데이트 때문이다.
  - 공통 변수 업데이트 구간(=임계구역)에 대한 동시 진입한다.
- 어떻게 해결하지?
  - <span class="clr-note">임계구역에 대한 동시 접근 방지(상호배타)하기</span>
  - 세마포를 사용한 상호배타 (mutual exclusion) 하기
  - 세마포: mutex.value = 1 (# of permit)

<br/>

```java
/****** 버퍼 ******/
import java.util.concurrent.Semaphore;

class Buffer {
  int[] buf;
  int size;
  int count;  // 버퍼의 현재 자원 크기
  int in; // 생산자 insert 할 버퍼의 위치
  int out;  // 소비자가 remove 할 버퍼의 위치
  Semaphore mutex;

  Buffer(int size) {
    buf = new int[size];
    this.size = size;
    count = in = out = 0;
    this.mutex = new Semaphore(1);
  }
  // 생산자
  void insert(int item) {
    /* check if buf is full */
    while (count == size)
    ;
    try {
      mutex.acquire();
      /****** S : 임계구역 *****/
      buf[in] = item;
      in = (in+1)%size; //circular
      count++;
      /****** E : 임계구역 *****/    
    } catch(InterruptedException) {}    
    mutex.release();
  }
  // 소비자
  int remove() {
    /* check if buf is empty */
    while (count == 0)
    ;
    try {
      mutex.acquire();
      /****** S : 임계구역 *****/
      int item = buf[out];
      out = (out+1)%size;
      count--;
      return item;
      /****** E : 임계구역 *****/
    } catch(InterruptedException) {}    
    mutex.release();
    return -1;
  }
}
```
<br/><br/>

- 하지만 또! <code>Busy-wait</code> 라는 문제가 있다.
  - insert의 <code>while (count == size);</code>와 remove의 <code>while (count == 0);</code> 로 인해 반복문에 붙잡히는 경우가 생긴다. cpu가 딴 일 못하고 붙잡힌다.
  - 생산자: 버퍼가 가득 차면 기다려야 = 빈(empty) 공간이 있어야 한다.
  - 소비자: 버퍼가 비면 기다려야 = 찬(full) 공간이 있어야 한다.
- 어떻게 해결하지?
  - 무한루프가 아니라 아예 감옥에 가둬버리자!
  - 세마포를 사용한 busy-wait 회피
  - 생산자: empty.acquire() // # of permit = BUF_SIZE
  - 소비자: full.acquire() // # of permit = 0

<br/>

```java
/****** 버퍼 ******/
import java.util.concurrent.Semaphore;

class Buffer {
  int[] buf;
  int size;
  int count;  // 버퍼의 현재 자원 크기
  int in; // 생산자 insert 할 버퍼의 위치
  int out;  // 소비자가 remove 할 버퍼의 위치
  Semaphore mutex;   // 임계구역에 1개만 들어가게 하려고
  Semaphore empty;
  Semaphore full;

  Buffer(int size) {
    buf = new int[size];
    this.size = size;
    count = in = out = 0;
    this.mutex = new Semaphore(1);
    this.empty = new Semaphore(size);
    this.full = new Semaphore(0);
  }
  // 생산자
  void insert(int item) {
    /* check if buf is full */
    while (count == size)
    ;
    try {
      empty.acuire();
      mutex.acquire();
      /****** S : 임계구역 *****/
      buf[in] = item;
      in = (in+1)%size; //circular
      count++;
      /****** E : 임계구역 *****/    
    } catch(InterruptedException) {}    
    mutex.release();
    full.release();
  }
  // 소비자
  int remove() {
    /* check if buf is empty */
    while (count == 0)
    ;
    try {
      full.acquire();
      mutex.acquire();
      /****** S : 임계구역 *****/
      int item = buf[out];
      out = (out+1)%size;
      count--;
      return item;
      /****** E : 임계구역 *****/
    } catch(InterruptedException) {}    
    mutex.release();
    empty.release();
    return -1;
  }
}
```

## Readers-Writers Problem
- 공통 데이터베이스
  - Readers: read data, never modify it
  - Writers: read data and modifiy it
  - 상호배타: 한 번에 한 개의 프로세스만 접근하는 것 효율성이 안 좋다.
- 효율성을 높이려면?
  - 데이터베이스 읽고 쓰기는 임계 구역 안에서 발생한다.
  - 그러므로 Writers은 당연히 상호 배타 해야한다.
  - 그러나 Reader1이 DB 읽고 있으면 Reader2도 읽을 수 있게 해줘야 효율성에 좋다!
- 변종
  - The first R/W problem (readers-preference) : 항상 readers에게 우선권을 주는 것. 예) 현재 Reader1이 DB를 읽고 있다고 가정해보자. Writer1의 경우는 상호 배타되어서 접근할 수 없다. 잠시 후 Reader2가 접근하면 Reader2는 늦게 왔음에도 불구하고 DB에 접근할 수 있다.
  - The second R/W problem (writers-preference) : writers에게 우선권 주는 것
  - The Third R/W problem : 우선권 아예 안 주는 것

## Dining Philosopher Problem

![process tree](/assets/images/2020-01-14-img/1.png)<br/>

> 5명의 철학자가 원탁에 앉아 있고 각자의 앞에는 스파게티가 있다. 그리고 양 옆에는 젓가락이 하나씩 있다. 각각의 철학자는 스파게티를 먹으려면 젓가락을 2개를 사용해야 하며, 다른 철학자에게 말을 걸 수 없고 젓가락을 빼앗을 수도 없다.
> <br/><br/> 이제 5명의 철학자 모두가 갑자기 배가 고파서 동시에 왼쪽 젓가락을 집어든다고 생각해보자. 철학자들은 젓가락을 공유할 수 없고 자신의 오른쪽에 앉은 철학자가 젓가락을 놓을 때까지 기다린다. 오른쪽 젓가락을 빼앗을 방법도 없어서 철학자들의 계속 대기만 한다...

- 식사하는 철학자 문제
  - 5명의 철학자, 5개의 젓가락, 생각 → 식사 → 생각 → 식사
  - 식사하려면 2개의 젓가락 필요
- 프로그래밍
  - 젓가락: 세마포 (# of permit = 1) -> 젓가락을 기준으로 2명 중 1명만 사용할 수 있으니깐 1로 한다.
  - 젓가락과 세마포에 일련번호: 0 ~ 4
  - 왼쪽 젓가락 → 오른쪽 젓가락

```java
/*********철학자*********/
import java.util.concurrent.Semaphore;
class Philosopher extends Thread {
  int id; // philosopher id
  Semaphore lstick, rstick; // left, right chopsticks
  Philosopher(int id, Semaphore lstick, Semaphore rstick) {
    this.id = id;
    this.lstick = lstick;
    this.rstick = rstick;
  }
  public void run() {
    try {
      while (true) {
        lstick.acquire();
        rstick.acquire();
        eating();
        lstick.release();
        rstick.release();
        thinking();
      }
    }catch (InterruptedException e) { }
  }

  void eating() {
    System.out.println("[" + id + "] eating");
  }
  void thinking() {
    System.out.println("[" + id + "] thinking");
  }
}
```

```java
/*********메인*********/
class Test {
  static final int num = 5; // number of philosphers & chopsticks
  public static void main(String[] args) {
    int i;
    /* chopsticks */
    Semaphore[] stick = new Semaphore[num];
    for (i=0; i<num; i++)
      stick[i] = new Semaphore(1);
    /* philosophers */
    Philosopher[] phil = new Philosopher[num];
    for (i=0; i<num; i++)
      phil[i] = new Philosopher(i, stick[i], stick[(i+1)%num]);
    /* let philosophers eat and think */
    for (i=0; i<num; i++)
      phil[i].start();
  }
}
```

- 위 코드는 프로그램이 돌다가 멈춘다.. 왜???!!!
  - 잘못된 결과: starvation -> 모든 철학자가 식사를 하지 못해 굶어 죽는 상황
  - 이유 = <span class="clr-note">교착상태 (deadlock)</span>

