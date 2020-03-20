# 운영체제 - 프로세스 관리

- Title : 운영체제 - 프로세스 관리
- Date : 2019-12-25
- Category: 운영체제

경성대학교 양희재 교수님 강의를 듣고 정리했습니다.

# # 프로세스

## 프로그램 vs 프로세스

<code class="codetainer">Process = task = job</code>  
게임 프로그램이 하드디스크에 있으면 아무 것도 할 수 없다. 게임을 하기 위해 더블 클릭하면 게임 프로그램이 메인 메모리에 올라와서 실행 된다. 이렇게 실행 중인 프로그램을 <code class="codetainer">프로세스</code>라고 한다

## 프로세스 상태

하나의 컴퓨터는 여러 개의 프로세스를 돌리고 CPU는 일정 시간을 나눠주어 이 프로세스들을 관리한다. 이 때, 프로세스는 <code class="codetainer">new, ready, running, waiting, terminated</code> 상태를 겪게 된다.

![프로세스 상태](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-25-img/process.png)<br/>

- new : 하드디스크의 프로그램이 메인 메모리에 올라왔을 때
- ready : 모든 초기화를 끝내고 실행 준비가 되었을 때
- running : 실제로 CPU에서 실행하고 있을 때
- waiting : 다시 CPU 할당 받을 때까지 기다릴 때
- terminated : 프로세스가 종료된 상태

- (1) : 프린트 출력 같은 I/O 발생 시 Waiting으로 감
- (2) : I/O 완료되면 다시 Ready로 감
- (3) : Time sharing system 에서는 일정 주기마다 프로세스 전환 인터럽트가 발생하는 데, 프로세스에게 주어진 CPU 사용 시간이 지나면 자동으로 이 프로세스는 Ready 상태로 가고 다른 프로세스가 running 상태가 된다.

# # PCB(Process Control Block)

사람으로 비유하면 주민등록증과 같고 프로세스를 잘 관리하기 위해서 PCB가 필요하다.

- 하나의 프로세스에 하나의 PCB가 있다.(PCB는 운영체제의 프로세스 관리 부서에 있음)
- <span class="clr-note">Task Control Block(TCB)</span> 라고도 한다.
- 프로세스에 대한 모든 정보가 들어있다.
- <span class="clr-note">process state(running, ready, waiting, …)</span>
- <span class="clr-note">PC</span> : 다시 CPU 돌아왔을 때, 그 당시의 PC 값이 필요하다.
- <span class="clr-note">registers</span> : CPU가 다른 프로그램 실행하다가 다시 돌아왔을 때, 어디서부터 다시 실행할 건지 알아야 한다.
- <span class="clr-note">MMU info (base, limit)</span> : 프로세스 스위칭 할 때마다 값을 바꿔줘야 한다
- <span class="clr-note">CPU time</span> : 현재까지 이 프로세스가 CPU 얼마나 사용했는 지
- <span class="clr-note">process id</span> : PID 프로세스마다 번호를 붙임
- <span class="clr-note">list of open files</span> : 프로세스가 지금 어떤 파일들을 사용하고 있는 지
- 등...

# # Queues

## Job Queue

하드디스크에 비해 메인 메모리 크기는 너무나 작아서 바로 메인 메모리에 못 올라가는 경우가 생긴다. 이 때 잡들은 큐에 줄 서게 되는 데, 이 것은 <code class="codetainer">Job Queue</code> 라고 한다. 큐에 있는 작업들을 어떤 순서대로 실행할 지를 정하는 것을 <code class="codetainer">Job scheduler(Long-term scheduler)</code> 라고 한다.

## Ready Queue

메인 메모리에 올라왔다고 바로 CPU 서비스 받는 것은 아니다. 또 <code class="codetainer">Ready Queue</code>에 줄 서야 한다. 여기의 잡들도 <code class="codetainer">CPU scheduler(short-term scheduler)</code>에 의해 순서가 정해진다.

## Device Queue

I/O, 하드 디스크 사용을 위해서도 <code class="codetainer">Device Queue</code>에 줄 선다. 여기 잡을 관리하는 것은 <code class="codetainer">Device scheduler</code>다.

# # Multiprogramming

## Degree of multiprogramming

메인 메모리에 몇 개의 프로세스가 올라가 있는 지.

## I/O-bound vs CPU-bound process

프로세스는 크게 <code class="codetainer">I/O-bound process</code>와 <code class="codetainer">CPU-bound process</code>로 나눌 수 있다. I/O-bound는 주로 입출력 작업을 하는 프로세스로 문서편집 프로그램이 대표적이다. CPU-bound는 연산 작업이 많아 주로 CPU를 사용하는 경우며 예시로 슈퍼 컴퓨터 쓰는 일기 예보 프로그램을 들 수 있다.

## Medium-term scheduler

short-term 보다는 적게 long-term 보다는 많게 일어난다. 운영체제는 메인 메모리를 감시하여 메모리에는 올라와있지만 아무 활동을 안하는 프로세스를 디스크에 쫒아낸다. 빈 메모리에는 새로운 프로그램을 올리거나 다른 프로세스에게 할당해준다. 이렇게 프로세스 이미지를 디스크로 쫒아내는 것을 <code class="codetainer">Swapping Out</code> 이라고 하며 반대로 다시 프로세스를 사용하기 위해 메인 메모리에 올리면 <code class="codetainer">Swapping In</code> 이라고 한다. 디스크를 프로세스 이미지를 쫒아내는 목적으로 사용하면 이를 <code class="codetainer">Swap</code> 이라고 한다.

## Context switching(문맥전환)

CPU는 메인 메모리의 여러 프로세스를 동시에 서비스할 수 없어서 Process1을 사용하다가 Process2로 넘어가야한다. 이처럼 프로세스를 전환하는 것을 문맥전환이라고 한다.

- <span class="clr-note">Scheduler</span> : 큐에서 기다리는 잡 중에 무엇을 선택해서 작업할 지
- <span class="clr-note">Dispatcher</span> : CPU가 프로세스1을 서비스하다가 스케줄러에 의해 프로세스2로 문맥전환을 하려는 상황을 생각해보자. 운영체제는 안전한 문맥전환을 위해 Dispatcher 프로그램을 통해 다음의 2가지 일을 한다. 첫째로, 프로세스1의 현재 상태와 MMU(BASE, LIMIT) 정보를 프로세스1을 위한 PCB에 저장한다. 그 다음, 프로세스2의 정보를 복원하여 이전에 중지된 위치에서 재개할 수 있게 한다.
- <span class="clr-note">Context switching overhead</span> : 정보를 저장하고 복원하는 과정에서 오버헤드(부담)가 생기기 마련이다. 컴퓨터는 이를 Context switching overhead라고 부른다.
