# 운영체제 - 운영체제 주요 서비스 간략하게!

- Title : 운영체제 - 운영체제 주요 서비스 간략하게!
- Date : 2019-12-25
- Category: 운영체제

경성대학교 양희재 교수님 강의를 듣고 정리했습니다.

# # 운영체제의 주요 서비스

- 프로세스 관리
- 주 기억장치 관리(메인 메모리 관리)
- 파일 관리(하드디스크의 파일)
- 보조기억장치 관리
- 입출력 장치 관리(프린트, 키보드, 마우스..)
- 네트워킹
- 보호
- 기타..

## 프로세스 관리

메인 메모리에서 실행 중인 프로그램을 프로세스라고 한다.

- 프로세스의 생성, 소멸(creation, deletion)
- 프로세스 활동 일시 중지, 활동 재개(suspend, resume)
- 프로세스간 통신(interprocess communication: IPC)
- 프로세스간 동기화(synchromization)
- 교착 상태 처리(deadlock handling)

## 주기억장치관리(메인메모리)

- 프로세스에게 메모리 공간 할당 (allocation)
- 메모리의 어느 부분이 어느 프로세스에게 할당되었는가 추적 및 감시
- 프로세스 종료 시 메모리 회수 (deallocation)
- 메모리의 효과적 사용
- 가상 메모리: 물리적 실제 메모리보다 큰 용량 갖도록

## 파일 관리

원래 디스크는 판 위의 Track과 sector의 집합이지만 파일이라는 논리적 관점으로 볼 수 있게 운영체제가 처리해준다.

- 파일의 생성과 삭제 (file creation & deletion)
- 디렉토리(directory)의 생성과 삭제 (또는 폴더 folder)- 기본동작지원: open, close, read, write, create, delete
- Track/sector – file 간의 매핑(mapping) – 백업(backup)

## 보조기억장치 관리

하드디스크, 플래시 메모리.

- 빈 공간 관리 (free space management) - 포맷 후 비워져있는 Block(섹터/트랙 집합)은 사용하다보면 비워져있는 공간, 사용되고 있는 공간으로 나눠진다. 이 공간을 관리하는 것
- 저장공간 할당 (storage allocation)
- 디스크 스케쥴링 (disk scheduling) : 어떻게 하면 헤더를 적게 움직이면서 원하는 트랙이나 섹터를 읽을 수 있을까?

## 입출력 장치 관리

- 장치 드라이브
- 입출력 장치의 성능 향상 : <span class="clr-note">buffering</span>(입출력 장치에서 읽은 내용을 일단 메모리에 가져오는 것, 한번 메모리에 가져오면 일단 빠르게 읽을 수 있음), <span class="clr-note">caching</span>, <span class="clr-note">spooling</span>(메모리 대신에 하드디스크를 중간 매체로 사용하는 것, 프린트로 출력할 내용을 디스크에 저장해서 cpu가 다른 일 할 수 있게)

## 시스템 콜

운영체제 서비스를 받기 위한 호출

- <span class="clr-note">Process</span>: end, abort, load, execute, create, terminate, get/set
  attributes, wait event, signal event
- <span class="clr-note">Memory</span>: allocate, free
- <span class="clr-note">File</span>: create, delete, open, close, read, write, get/set attributes
- <span class="clr-note">Device</span>: request, release, read, write, get/set attributes,
  attach/detache devices
- <span class="clr-note">Information</span>: get/set time, get/set system data
- <span class="clr-note">Communication</span>: socket, send, receive
