# 운영체제 - 이중모드, 하드웨어 보호

- Title : 운영체제 - 이중모드, 하드웨어 보호
- Date : 2019-12-25
- Category: 운영체제

경성대학교 양희재 교수님 강의를 듣고 정리했습니다.

# # 이중모드(Dual Mode)

## 이중모드의 등장 배경은?

하나의 서버 컴퓨터를 여러 사람이 동시에 사용하는 또는 한 사람이 여러 개의 프로그램을 동시에 사용하는 환경에서 한 사람의 고의/실수 프로그램은 전체에 영향을 끼치게 된다. 예를 들어, 한 사용자 프로그램에서 <code class="codetainer">STOP, HALT, RESET, SET_TIMER, SET_HW</code>등의 명령을 실행한다면 컴퓨터 전체가 영향을 받는다. 즉, <span class="clr-note">시스템에 치명적인 명령은 관리자만 실행할 수 있도록</span> 하기 위해 이중모드(사용자모드, 관리자모드)가 등장하게 된 것이다.

## 이중모드란?

<code class="codetainer">시스템(system) 모드 = 관리자(supervisor) 모드 = 모니터(monitor) 모드 = 특권(priviliged) 모드</code> 와 <code class="codetainer">사용자 모드</code>가 있다. 시스템 모드일 때, <code class="codetainer">STOP, HALT</code> 와 같은 <code class="codetainer">특권 명령</code>을 내릴 수 있고 사용자 프로그램에서는 치명적 명령을 사용 못하게 막는다.

## 이중모드는 어떻게 동작할까?

CPU에 있는 <code class="codetainer">레지스터</code>는 비트를 가지고 있으며 각 비트는 <code class="codetainer">carry, negative, zero, overflow</code> 발생을 알려주는 플래그 역할을 한다. 여기에 이중모드를 위한 비트를 추가하여 <span class="clr-note">시스템 모드면 1</span>을 <span class="clr-note">사용자 모드면 0</span>으로 표시한다. <br/><br/>
![이중모드](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-25-img/dual-mode.png)<br/>

- 운영체제 서비스를 실행될 때는 <span class="clr-note">관리자 모드</span>
- 사용자 프로그램 실행될 때는 <span class="clr-note">사용자 모드</span>
- 하드웨어/소프트웨어 인터럽트 발생하면 <span class="clr-note">관리자 모드</span>
- 운영체제 서비스가 끝나면 다시 <span class="clr-note">사용자 모드</span>

## 이중모드 예시

전원을 켰을 때 하드 디스크에 있는 운영체제가 메인 메모리에 올라가는 것은 **시스템 모드**에서 동작한다. 부팅이 끝나면 **사용자 모드**가 된다. 게임 아이콘을 더블클릭하여 인터럽트가 발생하면 다시 **시스템 모드**가 되어 인터럽트 서비스 루틴이 게임을 메인 메모리에 올리고 cpu가 실행할 수 있도록 한다. 다시 **사용자 모드**로 돌아오고 열심히 게임을 한다. 이제 게임 스코어를 하드디스크에 저장해보자. 게임 프로그램이 직접 하드디스크에 저장하는 것이 아니라 소프트웨어 인터럽트를 걸어 CPU가 지금 하던 일을 멈추고 운영체제의 ISR로 점프해서 실행하도록 한다. 이 때는 **시스템 모드**이다. ISR이 완료되면 레지스터 비트를 0으로 하여 **사용자 모드**로 만들고 다시 게임으로 돌아오게 된다.

하드웨어 인터럽트도 마찬가지다. **사용자 모드**에서 마우스를 움직이게 되면 CPU로 인터럽트를 보내 **시스템 모드**로 바꾼 후 ISR을 실행한다. 완료되면 다시 **사용자 모드**가 된다. 즉, <span class="clr-note">하나의 프로그램이 실행되는 동안 사용자 모드, 시스템 모드가 계속 반복된다.</span>

## 만약 사용자 프로그램에서 특권 명령을 내리면?

CPU는 레지스터의 모니터 비트를 확인하여 특권 명령이 사용자 모드일 때 내려지면 인터럽트를 발생시킨다. 운영체제의 인터럽트 서비스 루틴이 실행되고 이 ISR은 잘못된 시도를 한 프로그램을 강제 종료시켜 메모리에서 사라지게 한다.

# # 하드웨어 보호

## 입출력 장치 보호

사용자 A가 프린터를 사용 중인 상황에서 사용자 B가 프린트 출력 명령을 내리면 어떻게 될까? 운영체제가 없다면 A 사용자꺼 한 줄, B 사용자꺼 한 줄..식으로 출력이 될 것이다. 이같은 프린트 혼선, 리셋 등의 상황이 적절한가? 다른 예시로 하드디스크에 저장된 A의 정보를 B가 마음대로 읽고 쓰는 상황을 생각해보자. 이 것도 적절한가? 답은 <span class="clr-note">NO!!</span>

**어떻게 해야할까?** 컴퓨터는 바로 이렇게 해결했다.

입출력 명령인 <code class="codetainer">IN, OUT</code>을 특권 명령으로 하여 사용자가 직접 입출력 명령을 내릴 수 없도록 했다. 만약 사용자가 직접 내리면 <code class="codetainer">Privileged instruction violation</code>이기에 강제로 프로그램을 종료시킨다. 즉, 사용자가 입출력을 하기 위해서는 <code class="codetainer">소프트웨어 인터럽트</code>를 발생시켜 운영체제에 요청해야한다. 인터럽트에 의해 CPU가 시스템 모드로 전환하고 운영체제의 ISR을 실행하면 운영체제가 입출력을 대행한다. ISR에는 요청이 적절한 지를 확인하는 코드가 있어 올바른 요청에만 실행된다. 만약, 올바른 요청이 아니면 운영체제는 이를 거부한다. ISR이 완료되면 다시 사용자 모드로 복귀된다.

## 메모리 보호

메인 메모리에는 운영체제와 여러 개의 사용자 프로그램이 동시에 있기 때문에 다른 사용자 메모리 또는 운영체제에 접근하는 것을 막아야 한다. 보호하지 않으면 다른 사용자 정보나 프로그램을 해킹하거나 운영체제를 해킹하는 경우가 생긴다.

**어떻게 메모리를 보호할까?** 컴퓨터는 바로 이렇게 해결했다.

CPU와 메인 메모리 사이는 Bus로 연결되어있고 CPU -> 메모리는 <code class="codetainer">Address Bus</code>, 메모리 -> CPU는 <code class="codetainer">Data Bus</code>로 부른다. 컴퓨터는 다른 메모리 영역의 침범을 막기 위해 Address Bus에 <code class="codetainer">MMU(Memory Management Unit)</code>을 두었고 문지기 역할을 하도록 했다. MMU에는 <code class="codetainer">Base, Limit</code>가 있고 값 설정은 특권 명령으로 운영체제만 바꿀 수 있다. MMU는 Base~Limit 사이면 통과시키고 범위 밖이면 CPU로 인터럽트 신호를 발생시켜 해당 명령을 내린 프로그램을 종료시킨다. 이처럼 다른 사용자 또는 운영체제 영역 메모리에 접근하려는 것을 <code class="codetainer">Segment Violation</code> 이라고 한다.

## cpu 보호

한 사용자가 <code class="codetainer">while(n = 1)</code> 과 같은 명령을 실행하면 CPU를 계속 독점하여 다른 사용자의 프로그램을 실행할 수 없게 된다. 이를 막기 위해 CPU 보호가 필요한 것이다. <span class="clr-note">컴퓨터는 Timer를 두어 일정 시간 경과 시 타이머 인터럽트를 발생시키는 방법으로 해결했다.</span> 일정 신호 주기로 타이머가 CPU에 인터럽트를 걸게 되면 CPU는 ISR을 실행시켜 프로그램이 CPU를 너무 오랜 시간 독점하고 있는 지를 확인한다. 독점하고 있다고 판단되면 CPU를 강제 전환시켜 다른 프로그램을 실행토록 한다.
