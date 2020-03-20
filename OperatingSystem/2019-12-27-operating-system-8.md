# 운영체제 - 프로세스 생성과 종료 및 쓰레드

- Title : 운영체제 - 프로세스 생성과 종료 및 쓰레드
- Date : 2019-12-27
- Category: 운영체제

경성대학교 양희재 교수님 강의를 듣고 정리했습니다.

# # Process Creation(프로세스 생성)

## 프로세스는 프로세스에 의해 만들어진다!

부팅 후 OS가 프로세스를 만들면 그 프로세스에 자식 프로세스가 만들어지고 또 자식의 자식 프로세스가 만들어진다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-27-img/1.png)<br/>

- 부모 프로세스(Parent process)
- 자식 프로세스(Child process)
- 형제 프로세스(Sibling process) : 부모가 같은 프로세스
- 프로세스 트리 (process tree) = 족보, 가계도

## Process Identifier (PID)

프로세스의 유니크 번호로 주민등록번호와 비슷한 개념이다. 절대 PID 중복되면 안됀다. cf) PPID : 부모 프로세스의 PID

## 프로세스 생성

- fork() system call – 부모 프로세스 복사해서 새로운 프로그램을 만든다.
- exec() – 새로 만들어진 프로그램의 실행파일을 메모리로 가져온다.

# # Process Termination(프로세스 종료)

- exit() system call - 프로세스를 종료시킨다. 해당 프로세스가 가졌던 모든 자원(메모리, 열었던 파일, 프린터같은 입출력 장치 등)을 회수해서 O/S에게 반환한다.

# # 쓰레드(Thread)

쓰레드란? <span class="clr-note">프로그램 내부의 흐름, 맥</span>이다.

```java
class Test {
  public static void main(String[] args) {
    int n = 0;
    int m = 6;
    System.out.println(n+m);
    while (n < m) n++;
    System.out.println("Bye");
  }
```

이처럼 하나의 프로그램에는 보통 맥(쓰레드)이 하나 있으나 어떤 프로그램은 맥(쓰레드)이 여러 개가 있다. 하나의 프로그램에 2개 이상의 맥이 있으면 <span class="clr-note">다중 쓰레드 (Multithreads)</span>라고 한다. 맥이 빠른 시간 간격으로 스위칭되기 때문에 여러 맥이 동시에 실행되는 것처럼 보인다.(=<code class="codetainer">Concurrent</code> 동시성) cf) <code class="codetainer">simultaneous</code>는 진짜로 동시에 실행되는 경우를 말하며 CPU가 하나면 일어날 수 없는 일이다.

## 예시

Web browser(화면 출력하는 쓰레드 + 데이터 읽어오는 쓰레드), Word processor(화면 출력하는 쓰레드 + 키보드 입력 받는 쓰레드 + 철자/문법
오류 확인 쓰레드), 음악 연주기, 동영상 플레이어, Eclipse IDE

우리가 이전 포스팅에서는 <code class="codetainer">Process1 -> Process2 -> Process3...</code>로 프로세스가 스위칭된다고 했지만 사실 운영체제는 다중 스레드를 지원하고 있어서 다음과 같은 흐름을 가진다. <code class="codetainer">Thread1 of Process1 -> Thread2 of Process1 -> Thread1 of Process2 -> Thread2 of Process2 -> Thread3 of Process2 .....</code>

## 쓰레드 구조

![process](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-27-img/2.JPG)<br/>

- 프로세스의 메모리 공간 공유 (code, data)
- 프로세스의 자원 공유 (file, i/o, …)
- 비공유: 개별적인 PC(program counter), SP(stack pointer), registers, stack

![process](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-27-img/3.JPG)<br/>

**vs 프로세스** <br/>
프로세스는 각각 독립된 메모리 영역(Code, Data, Stack, Heap)을 할당받는다. 즉, 각 프로세스는 별도의 주소 공간에서 실행되고 한 프로세스는 다른 프로세스의 변수나 자료구조에 접근할 수 없다. 다른 프로세스의 자원에 접근하려면 <code class="codetainer">프로세스 간의 통신(IPC, inter-process communication, 파일, 소켓, 파이프...)</code>을 사용해야 한다.

# # 잠깐 컴퓨터 구조 지식을 채워보자.

## Register(레지스터)

CPU 내부에서 처리할 명령어나 연산의 중간 결과값 등을 일시적으로 기억하는 임시 기억장소다.

- 데이터 레지스터(data register) : CPU가 처리하는 데이터를 임시로 저장
- <code class="codetainer">주소 레지스터(address register)</code> : 기억장치를 액세스할 주소를 저장하며 포인터(Pointer)라고도 한다. 스택 포인터(SP, Stack Pointer), 베이스 포인터(BP, Base Pointer), 인덱스 포인터(IX, Index Pointer)가 있다.
- 범용 레지스터(GPR) :주소 레지스터 혹은 데이터 레지스터로 사용될 수 있는 레지스터

## PC

프로그램 카운터(Program counter, PC)는 마이크로프로세서(중앙 처리 장치) 내부에 있는 레지스터 중의 하나로서, 다음에 실행될 명령어의 주소를 가지고 있어 실행할 기계어 코드의 위치를 지정한다. 때문에 명령어 포인터라고도 한다.

## 버스

CPU, 메모리, I/O 장치 등과 상호 필요한 정보를 교환하기 위해 연결된 공동의 전송선이다.
