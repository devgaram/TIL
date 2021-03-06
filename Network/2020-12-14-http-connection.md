# HTTP 커넥션(part 1)
- Title : HTTP 커넥션(part 1)
- Date : 2020-12-14
- Category : Network

## `http://www.github.com:80/devgaram` URL을 입력받은 브라우저가 하는 일
> 전 세계 HTTP 통신은 컴퓨터와 네트워크 장비에서 널리 쓰이고 있는 패킷 교환 네트워크 프로토콜들의 계층화된 집합인 TCP/IP를 통해 이루어진다.
> 세계 어디서든 클라이언트 애플리케이션은 서버 애플리케이션으로 TCP/IP 커넥션을 맺을 수 있다.

1. 브라우저가 `www.github.com`라는 호스트 명을 추출한다.
2. 브라우저가 호스트 명에 대한 IP 주소, 192.30.252.153를 찾는다.
3. 브라우저가 포트 번호, 80을 얻는다.
4. 브라우저가 192.30.252.153의 80포트로 TCP 커넥션을 생성한다.
5. 브라우저가 서버로 HTTP GET 요청 메시지를 보낸다.
6. 브라우저가 서버에서 온 HTTP 응답 메시지를 읽는다.
7. 브라우저가 커넥션을 끊는다.

## TCP 커넥션
> HTTP 커넥션은 몇몇 사용 규칙을 제외하고는 TCP 커넥션에 불과하며 TCP는 HTTP에게 신뢰할 만한 통신 방식을 제공한다.

- TCP 커넥션은 **<발신지 IP주소, 발신지 포트, 수신지 IP 주소, 수신지 포트>** , 이 네가지 값으로 생성되며, 네가지가 모두 동일한 커넥션은 존재할 수 없다.
- IP 주소가 컴퓨터, 포트 번호가 애플리케이션으로 생각하면 된다.

### 프로토콜 스택
HTTP - 애플리케이션 계층 | TCP - 전송 계층 | IP - 네트워크 계층 | Network Interfaces - 데이터링크 계층

**HTTP**

|HTTP 프로토콜|
|:---:|
|HTTP|
|TCP|
|IP|
|Network Interfaces|

**HTTPS**    
HTTP에 보안 기능을 더한 것으로 SSL 또는 TLS라고 불리기도 하며 HTTP와 TCP 사이에 있는 암호화 계층이다.

|HTTPS 프로토콜|
|---|
|HTTP| 
|TLS 또는 SSL|
|TCP|
|IP|
|Network Interfaces|

### 전송 과정

1. HTTP가 메시지를 전송하고자 현재 연결되어 있는 TCP 커넥션을 통해서 메시지 데이터의 내용을 순서대로 보낸다.
2. TCP는 세그먼트라는 단위로 데이터 스트림을 잘게 나눈다. 각 세그먼트를 IP 패킷(혹은 IP 데이터그램)에 담아서 하나의 IP 주소에서 다른 IP 주소로 전달한다.

### IP 패킷에 포함되는 정보
- IP 패킷 헤더(20byte): 발신지와 목적지의 IP 주소, 크기, 기타 플래그 가짐
- TCP 세그먼트 헤더(20byte): TCP 포트 번호, TCP 제어 플래그, 데이터 순서와 무결성을 검사하기 위해 사용하는 숫자 값 가짐
- TCP 데이터 조각(0혹은 그 이상의 바이트)

## TCP 소켓 프로그래밍

소켓 API를 사용하면 TCP 종단(endpoint) 데이터 구조를 생성하고, 원격 서버의 TCP 종단에 그 종단 데이터 구조를 연결하여 데이터 스트림을 읽고 쓸 수 있다.
TCP API는 기본적인 네트워크 프로토콜의 핸드셰이킹, 그리고 TCP 데이터 스트림과 IP 패킷 간의 분할 및 재조립에 대한 모든 세부사항을 외부로부터 숨긴다.

### 클라이언트와 서버가 TCP 소켓 인터페이스를 사용하여 상호작용 하는 방법

|클라이언트|서버|
|---|---|
|| 1. 새로운 소켓을 만든다|
||2. 80 포트로 소켓을 묶는다|
||3. 소켓 커넥션을 허가한다(listen)|
||4. 커넥션을 기다린다(accept)|
|1. IP 주소와 포트를 얻는다.||
|2. 새로운 소켓을 생성한다.||
|3. 서버의 IP포트로 연결한다(connect)||
||5. 애플리케이션에 커넥션 통지|
|4. 성공적으로 연결 |6. 요청을 읽기 시작한다(read)|
|5. HTTP 요청을 보낸다(write)|7. HTTP 요청 메시지를 처리한다.|
|6. HTTP 응답을 기다린다(read)||
||8. HTTP 응답을 보낸다(write)|
|7. HTTP 응답을 처리한다||
|8. 커넥션을 닫는다(close)|
||9. 커넥션을 닫는다(close)|


참고 책 - HTTP 완벽 가이드
