# REST API

- 웹
- 2020년 03월 16일

## ✨ REST API 란?

REST(Representational State Transfer)는 웹처럼 HTTP 기반의 시스템에 필요한 자원(리소스, DBMS 데이터, 이미지, 동영상, 서비스..등)에 접근하는 방식을 정해놓은 아키텍쳐다. 

### REST 구성

- 자원(RESOURCE) - URI
    - 리소스명은 동사보다는 명사를 사용
- 행위(Verb) - HTTP METHOD(GET, POST, PUT, DELETE)
- 표현(Representations)

### REST 특징

- **클라이언트/서버 구조** : REST 서버는 API 제공, 클라이언트는 사용자 인증이나 컨텍스트(세션, 로그인 정보)등을 직접 관리하는 구조로 각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로 간 의존성이 줄어든다.
- **무상태성(Stateless)** : 클라이언트 컨텍스트(세션, 쿠키)는 서버에 저장되어서는 안 된다. API 서버는 단순히 들어오는 요청만을 처리한다.
- **캐시 처리 가능(Cacheable)** : HTTP가 가진 캐싱 기능 적용이 가능하다. 클라이언트 응답을 캐싱할 수 있어야 한다.
- **계층화(Layered System)** : REST 서버는 다중 계층으로 구성될 수 있다. 하지만 클라이언트는 REST 서버에 직접 연결되었는지, 또는 중간 서버를 통해 연결되었는 지를 알 수 없게 해야한다. 중간 서버는 로드 밸런싱 기능이나 공유 캐시 기능을 제공함으로써 시스템 규모 확장성을 향상시키는 데 유용하다.
- **유니폼 인터페이스(Uniform)** : URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일을 말한다.

### URI 설계 시 주의할 점

- 슬래시 구분자(/)는 계층 관계를 나타내는 데 사용한다.
- URI 마지막 문자로 슬래시(/)를 포함하지 않는다.
- 하이픈(-)은 URI 가독성을 높이는데 사용한다.
- 밑줄(_)은 URI에 사용하지 않는다.
- URI 경로에는 소문자가 적합하다.
- 파일 확장자는 URI에 포함시키지 않는다.

### 리소스 간의 관계 표현하는 방법

사용자가 좋아하는 디바이스 목록 가져오기

    GET : /users/{userid}/likes/devices

### 자원을 표현하는 Collection과 Document

- Collection: 문서들의 집합, 객체들의 집합
- Document: 문서, 객체

예시로 이해하기

- sports, players: 컬렉션
- soccer, 13: 다큐먼트

    http:// restapi.example.com/sports/soccer/players/13

**참고 99.9%**

[REST API 제대로 알고 사용하기 : TOAST Meetup](https://meetup.toast.com/posts/92)