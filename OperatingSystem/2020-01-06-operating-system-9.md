# 운영체제 - 프로세스 동기화 1

- Title : 운영체제 - 프로세스 동기화 1
- Date : 2020-01-06
- Category: 운영체제

> 1. 프로세스 동기화란?
> 2. 임계구역이란?
> 3. 세마포란?

# 프로세스 동기화(Process Synchronization, Thread Synchronization)

프로세스들이 서로 공유하는 데이터에 동시에 접근할 때 순서대로 실행하여 **일관성있는 데이터를 유지**하는 것

- Independent : 프로세스1과 프로세스2가 관계가 없을 때
- Cooperating : 다른 프로세스에게 영향을 미치거나 다른 프로세스로부터 영향을 받을 때
- 프로세스 간 통신 : 전자 우편, 파일 전송
- 프로세스 간 자원 공유 : 메모리 상의 자료, 데이터베이스 등
- 실생활 예 : 명절 기차표 예약, 대학 온라인 수강신청, 실시간 주식 거래

## 프로세스/쓰레드 동기화를 하려면?

- 임계구역 문제 해결(틀린 답이 나오지 않도록)
- 프로세스 실행 순서 제어(원하는 대로)
- busy wait 등 비효율성은 제거

## 동기화 도구

- 세마포(Semaphores)
- 모니터(Monitors)
- Misc.

## 은행 계좌 예시

아래의 코드는 공통 변수(balance)에 대해 동시 업데이트를 수행하여 0이 아닌 잘못된 결과값이 출력된다. 이는 한번에 한 쓰레드만 업데이트하도록 하여 해결할 수 있다. -> **임계구역 문제**

```java
class Test {
public static void main(String[] args) throws InterruptedException {
  BankAccount b = new
  BankAccount();
  Parent p = new Parent(b);
  Child c = new Child(b);
  p.start();
  c.start();
  p.join();
  c.join();
  System.out.println( "\nbalance = " + b.getBalance());
  }
}
```

```java
class BankAccount {
  int balance;
  void deposit(int amount) {
    int temp = balance + amount;
    System.out.print("+")
    balance = temp; // 임계구역
  }
  void withdraw(int amount) {
    int temp = balance - amount;
    System.out.print("-")
    balance = temp; // 임계구역
  }
  int getBalance() {
    return balance;
  }
}

```

```java
class Parent extends Thread {
  BankAccount b;
  Parent(BankAccount b) {
    this.b = b;
  }
  public void run() {
    for (int i=0; i<100; i++)
    b.deposit(1000);
  }
}
class Child extends Thread {
  BankAccount b;
  Child(BankAccount b) {
    this.b = b;
  }
  public void run() {
    for (int i=0; i<100; i++)
    b.withdraw(1000);
  }
}
```

# 임계구역 문제(The Critical-Section Problem)

## 임계구역(Critical Section)

여러 개의 스레드로 구성된 시스템에서 각 스레드가 같이 사용하는 변수, 테이블, 파일을 바꾸는 코드 영역을 임계구역이라고 한다. 예) 은행 계좌 예시에서 balance 값을 바꾸는 코드가 임계구역이다.

## 임계구역 문제 해결방법 - 아래 3가지 다 만족해야한다!

- Mutual exclusion (상호배타): 오직 한 쓰레드만 진입<br/>예) Parent 스레드가 임계구역 실행 중에는 Child 스레드는 임계구역에 들어갈 수 없다.
- Progress (진행): 진입 결정은 유한 시간 내<br/>예) 임계구역에 어떤 스레드가 먼저 진입할지에 대한 결정은 유한 시간 내에 결정되어야 한다.
- Bounded waiting (유한대기): 어느 쓰레드라도<br/>예) 어느 스레드라도 기다리고 있으면 유한한 시간내에 임계구역에 들어갈 수 있다.

# 세마포 (Semaphore)

동기화 문제 해결을 위한 소프트웨어 도구로 <span class="clr-note">정수형 변수 + 두 개의 동작 (P, V)</span>으로 이루어져있다.

## 동작

- P: Proberen (test) → **acquire()**
- V: Verhogen (increment) → **release()**

## 구조

- acquire() : 임계구역 전에 호출하여 value가 조건에 맞으면 **프로세스(or 스레드)를 list(큐)에 넣는다.** 큐에 들어간 프로세스는 다른 프로세스에 의해 release()가 호출될 때까지 Block된다. 즉 임계구역을 실행할 수 없게 된다. (Block이 된다 == Ready Queue에 들어가지 못한다)
- release() : value 조건에 맞으면 list에서 **프로세스를 꺼내서 깨운다.** 깨운다는 것은 Ready Queue(cpu 서비스 기다리는 줄)에 넣는 다는 것을 의미한다.

```java
class Semaphore {
  int value; // number of permits

  Semaphore(int value) {
  ... }
  void acquire() {
    value--;
    if (value < 0) {
      add this process/thread to list;
      block;
    }
  }
  void release() {
    value++;
    if (value <= 0) {
      remove a process P from list;
      wakeup P;
    }
  }
}

```

## 세마포의 사용 - 상호 배타(Mutual exclusion)

sem.value = 1로 두어 임계구역에는 프로세스 한 개만 들어갈 수 있도록 한다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2020-01-06-img/1.png)<br/>

아래 코드는 은행 계좌 예시의 BankAccount 클래스를 수정한 것으로 임계구역 문제를 해결한다.

```java
import java.util.concurrent.Semaphore;

class BankAccount {
  int balance;
  Semaphore sem;
  public BankAccount () {
    this.sem = new Semaphore(1)
  }
  void deposit(int amount) {
    try {
      sem.acquire()
    } catch(InterruptedException) {}

    int temp = balance + amount;
    System.out.print("+")
    balance = temp;
    sem.release()
  }
  void withdraw(int amount) {
    try {
      sem.acquire()
    } catch(InterruptedException) {}
    int temp = balance - amount;
    System.out.print("-")
    balance = temp;
    sem.release()
  }
  int getBalance() {
    return balance;
  }
}

```

1. Parent 프로세스가 deposit()을 호출하면 acquire() 메소드가 실행된다. 이는 value를 0으로 만드나 조건이 false라 바로 빠져나와 임계구역을 실행한다.
2. 문맥 전환에 의해 Child 프로세스의 witdhdraw()가 호출되면 acquire()이 호출된다. value를 -1로 만들어 조건이 true가 되어 Child 프로세스는 큐에 들어가고 Block 된다. 즉, 임계구역을 못 간다.
3. Parent 프로세스가 임계구역 실행을 완료한 후 release()를 호출하면 큐의 프로세스 하나를 뺀다. 즉, Child 프로세스를 깨워 Ready Queue로 보낸다.

## 세마포의 사용 - 프로세스 실행 순서 제어(Ordering)

CPU 스케줄링 알고리즘에 관련없이 P1에 들어간 S1 코드가 P2의 S2 코드보다 먼저 실행되게 하고 싶을 때? 세마포를 사용하자!

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2020-01-06-img/2.png)<br/>

항상 입금이 먼저 되게 BankAccount 클래스를 수정해보자.

```java
import java.util.concurrent.Semaphore;

class BankAccount {
  int balance;
  Semaphore sem;
  Semaphore sem2;
  public BankAccount () {
    this.sem = new Semaphore(1);
    this.sem2 = new Semaphore(0)
  }
  void deposit(int amount) {
    try {
      sem.acquire()
    } catch(InterruptedException) {}

    int temp = balance + amount;
    System.out.print("+")
    balance = temp;
    sem.release()
    sem2.release()
  }
  void withdraw(int amount) {
    try {
      sem2.acquire()
      sem.acquire()
    } catch(InterruptedException) {}
    int temp = balance - amount;
    System.out.print("-")
    balance = temp;
    sem.release()
  }
  int getBalance() {
    return balance;
  }
}

```

이제 입출금 교대로 되게 BankAccount 클래스를 수정해보자.

```java
import java.util.concurrent.Semaphore;

class BankAccount {
  int balance;
  Semaphore sem;
  Semaphore dsem;
  Semaphore wsem;
  public BankAccount () {
    this.sem = new Semaphore(1);
    this.dsem = new Semaphore(0);
    this.wsem = new Semaphore(0);
  }
  void deposit(int amount) {
    try {
      sem.acquire();
    } catch(InterruptedException) {}

    int temp = balance + amount;
    System.out.print("+")
    balance = temp;
    sem.release();
    wsem.release();
    try {
      dsem.acquire();
    } catch(InterruptedException) {}

  }
  void withdraw(int amount) {
    try {
      wsem.acquire();
      sem.acquire();
    } catch(InterruptedException) {}
    int temp = balance - amount;
    System.out.print("-")
    balance = temp;
    sem.release();
    dsem.release();
  }
  int getBalance() {
    return balance;
  }
}

```
