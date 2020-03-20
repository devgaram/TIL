# 운영체제 - CPU Scheduling

- Title : 운영체제 - CPU Scheduling
- Date : 2019-12-26
- Category: 운영체제

경성대학교 양희재 교수님 강의를 듣고 정리했습니다.

# # CPU 스케줄링 관련 용어 정리

## <span class="clr-note">Preemptive(선점) vs Non-preemptive(비선점)</span>

<code class="codetainer">Preemptive(선점)</code>은 I/O 인터럽트, 프로세스 실행 완료의 경우가 아님에도 CPU 서비스를 받고 있는 프로세스를 강제로 쫒아내 다른 프로세스를 실행하는 것을 말한다. <span class="clr-grey">예) 응급실에서 응급 환자를 우선 치료하는 것</span>

<code class="codetainer">Non-preemptive(비선점)</code>은 프로세스 실행 중 I/O 인터럽트가 발생하거나 프로세스 실행 완료되기 전에는 절대 문맥전환 일어나지 않는 것을 말한다. <span class="clr-grey">예) 은행 </span>

## <span class="clr-note">Scheduling criteria</span>

스케줄링의 성능 판단을 위한 척도

- CPU Utilization (CPU 이용률, %): CPU가 얼마나 쉬지 않고 일하는 가?
- Throughput (처리율, jobs/sec) : 시간 당 몇개의 작업을 처리하는 가?
- Turnaround time (반환시간, sec) : 프로세스의 실행 시작 후, 완전히 종료되기까지의 시간. 프로세스는 보통 한번에 실행이 완료되지 않고 문맥전환이 발생한다는 사실을 기억해두고 반환시간을 이해하자.
- Waiting time (대기시간, sec) : CPU 서비스 받기 위해 <code class="codetainer">Ready Queue</code>에서 얼마나 기다렸나?
- Response time (응답시간, sec) : 대화형 시스템에서 가장 중요한 척도로 명령을 내렸을 때, 처음 응답이 나오는 데까지 걸린 시간이다

# # CPU 스케쥴링 알고리즘

## <span class="clr-note">1. First-Come, First-Served (FCFS)</span>

![FCFS](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/1.png)<br/>
**AWT = (0+24+27)/3 = 17 msec**<br/>
**최적은 3 msec!**
<br/><br/><br/>

- 먼저 들어온 순서대로 서비스 받는다(단순, 공평)
- Average Waiting Time(AWT) 면에서 좋지 않을 수 있다.
- Convoy Effect (호위효과) : <code class="codetainer">CPU Burst Time</code>이 긴 프로그램이 앞에 있으면 뒤 프로그램이 영향을 크게 받음
- Non-preemptive(비선점)스케줄링이다.

## <span class="clr-note">2. Shortest-Job-First (SJF)</span>

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/2.png)<br/>
**AWT = (3+16+9+0)/4 = 7 msec**
<br/><br/><br/>
AWT 줄이는 면에서는 제일 좋지만 비현실적이다. 실제로 프로세스가 CPU를 얼마나 썼는 지 계산하려면 과거에 CPU를 얼마나 사용했는 지를 기억하고 있어야 하고 미래는 이정도 쓰겠다 계산하여 예측해야한다. 또, 이 예측이 맞는 지 일일이 계산해야한다. 실제로는 문맥 전환이 잦기 때문에 이런 작업은 <span class="clr-note">오버헤드를 증가시킨다.</span>

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/3.png)<br/>
**Preemptive: AWT = (9+0+15+2)/4 = 26/4 = 6.5 msec**<br/>
**Nonpreemptive: 7.75 msec**
<br/><br/><br/>
SJF 정책은 Preemptive(선점)과 Non-preemptive(비선점) 두 가지로 만들 수 있고 Preemptive SJF는 <code class="codetainer">Shortest-Remaining-Time-First (최소잔여시간 우선)</code> 라고도 한다.

## <span class="clr-note">3. Priority Scheduling</span>

정수 값으로 우선 순위를 정하고 우선 순위가 높을 프로세스를 먼저 서비스 해주는 방법이다. 일반적으로 값이 낮을 수록 높은 우선 순위를 가진다.

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/4.png)<br/>
**AWT = 8.2 msec**
<br/><br/>

1. 우선순위 정하는 방법<br/>

내부적

- time limit : time limit 짧은 프로세스
- memory requirement: 메모리 작게 차지하는 프로세스
- i/o to CPU burst : I/O 처리 시간 길고 CPU 처리 시간 짧은 프로세스

외부적

- amount of funds being paid : 돈 많이 낸 프로세스 <span class="clr-grey">예) 컴퓨터를 여러 학과가 쓴다면 등록금 많이 낸 학과가 우선순위</span>
- political factors : 정치적 요소 <span class="clr-grey">예) 학생</span>

2. Preemptive, Nonpreemptive 둘다 가능하다.<br/>

3. 문제점 - starvation (기아) <br/>

어떤 프로세스의 우선 순위가 너무 낮아서 아무리 기다려도 CPU 서비스를 받지 못하는 상황을 <code class="codetainer">starvation(기아)</code>라고 한다. 외부에서 그 프로세스보다 우선 순위가 높은 프로세스가 계속 들어올 경우에 발생한다. 해결법으로 <code class="codetainer">againg</code>을 쓰는 데, 이는 운영체제가 주기적으로 Ready Queue를 조사해서 어떤 JOB이 너무 오래 기다리고 있다면 점진적으로 우선순위를 높여주는 방법이다.

## <span class="clr-note">4. Round-Robin</span>

- Time-sharing system (시분할/시공유 시스템)에서 쓴다.
- Time quantum 시간양자 = time slice (10 ~ 100msec) 주기로 메인 메모리의 프로세스를 실행한다. 만약 Time quantum이 100msec면 1초에 100번 문맥전환이 일어난다는 것
- 오직 Preemptive scheduling 정책만 있다.
- Time quantum에 의존적이므로 좋은 성능을 위해서는 Time quantum의 크기를 잘 고려해야한다.

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/5.png)<br/>

위와 같이 Time quantum을 무한대로 하면 FCFS 알고리즘과 같아진다. 반대로 0으로 수렴시키면 Process sharing이라고 하는 데, 이는 스위칭이 빈번하게 일어나 프로세스들이 거의 동시에 도는 것처럼 느껴진다. 문맥전환이 빈번하므로 Context switching overhead가 크다.

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/6.png)<br/>
위와 같이 Time quantum 크기에 따라 Average turnaround time (ATT)가 어떻게 되는 지 계산을 해보라.

## <span class="clr-note">5. Multilevel Queue Scheduling</span>

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/7.png)<br/>

프로세스를 성격에 따라 그룹화하여 여러 개의 큐에 그룹별로 JOB을 줄 세우는 방법이다. 각 큐는 절대적 우선 순위를 가지고 있으며 CPU 시간을 큐에 차등으로 배분한다. 또한, 각 큐는 독립된 스케줄링 정책을 가진다.

## <span class="clr-note">6. Multilevel Feedback Queue Scheduling</span>

![SJF](https://raw.githubusercontent.com/devgaram/TIL/master/OperatingSystem/images/2019-12-26-img/8.png)<br/>

복수 개의 큐를 둔다는 점은 Multilevel Queue Scheduling와 비슷하나 모든 프로세스가 하나의 입구로 진입한다는 차이점이 있다. 프로세스가 너무 많은 CPU를 사용하면 다른 큐에 줄 세우고 기아 상태가 우려되는 JOB이 있다면 우선순위가 높은 큐에 다시 줄 세우는 방식이다.
