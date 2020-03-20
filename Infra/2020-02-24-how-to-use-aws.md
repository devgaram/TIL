# AWS 연습하자 1탄 - AWS EC2 인스턴스에 Jenkins 서버 구축하기

- Title : AWS 연습하자 1탄 - AWS EC2 인스턴스에 Jenkins 서버 구축하기
- Date : 2020-02-24
- Category: Infra

AWS 연습하기 1탄에서는 AWS EC2에 인스턴스를 생성하고 Jenkins를 구축하는 과정을 다뤄보겠습니다. AWS에서 제공하는 [가이드](https://d1.awsstatic.com/Projects/P5505030/aws-project_Jenkins-build-server.pdf)를 참고하여 실습을 진행했습니다.

# AWS EC2 인스턴스를 생성하기

먼저 [Amazon EC2 console](https://console.aws.amazon.com/ec2/)로 이동해서 우측 상단의 Region을 서울로 설정합니다.

인스턴스 생성에 앞서 키 페어부터 생성하도록 하겠습니다.
왼쪽 네비게이션 바에서 **네트워크 및 보안 -> 키 페어 -> 키 페어 생성** 을 클릭합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/1.png)

이름을 입력 후 파일 형식은 pem을 선택하고 키 페어 생성 버튼을 누르면 .pem 파일 형식의 개인키가 다운로드됩니다. 인스턴스 생성, 연결 시 필요하므로 **꼭 잘 보관해두세요**.

> 윈도우 10은 openssh가 내장되어있어 ssh 명령어를 사용할 수 있습니다. ssh를 지원하지않는 경우라면 ppk를 사용하시면 됩니다.

이제 인스턴스를 생성하겠습니다. **EC2 대시보드 -> 인스턴스 시작 섹션 -> 인스턴스 시작** 을 선택합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/2.png)

**단계 1: AMI 선택** 에서 Amazon Linux AMI 프리 티어 사용 가능한 AMI를 선택하세요.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/3.png)

**단계 2: 인스턴스 유형 선택** 에서 디폴트로 선택된 t2.micro를 선택한 후 다음: 인스턴스 세부 정보 구성을 선택합니다.

**단계 3: 인스턴스 세부 정보 구성** 에서 디폴트 값을 그대로 사용하겠습니다. 다음:스토리지 추가를 선택합니다.

**단계 4: 스토리지 추가** 에서 프리티어는 최대 30GB의 EBS 범용(SSD) 또는 마그네틱 스토리지를 사용할 수 있습니다. 크기 항목에 8로 되어있는 것을 30으로 수정한 후 다음:태그 추가를 클릭합니다.

**단계 5: 태그 추가** 에서 여러 인스턴스를 태그로 검색이나 그룹짓기 위해 아래와 같이 태그를 추가합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/4.png)

**단계 6: 보안 그룹 구성** 에서 인스턴스 방화벽 역할을 하는 보안 그룹을 설정하겠습니다. 보안그룹에서 인바운드와 아웃바운드 트래픽을 인스턴스 레벨에서 컨트롤할 수 있습니다.

우선 디폴트로 있는 SSH 유형의 소스를 내 IP로 변경합니다. SSH의 소스를 모든 IPv4(0.0.0.0/0)으로 설정하는 것은 보안상 추천하지않습니다.

HTTP, HTTPS 유형을 추가하고 소스를 0.0.0.0/0으로 설정합니다. 외부에서 이 서버로 접속하려면 꼭 설정해야합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/5.png)

**검토 및 시작 -> 시작 -> 기존 키 페어 선택 또는 새 키 페어 생성 모달** 을 띄웁니다. 앞에서 키 my-key-pair 이름으로 키 페어를 생성해놨던 것을 여기서 사용할 것입니다. 아래와 같이 선택한 후 인스턴스 시작을 클릭합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/6.png)

인스턴스 상태가 running이 되면 정상적으로 인스턴스 생성 및 실행이 완료된 것입니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/7.png)

# Jenkins 설치하기

방금 생성한 ec2 인스턴스에 젠킨스를 구축하려면 보안 그룹을 편집해야합니다.

보안그룹에서 HTTPS 프로토콜을 제거하고 사용자 지정 TCP 규칙을 추가하겠습니다.

스크롤을 오른쪽으로 해보면 보안그룹이 보입니다. 바로 밑의 링크를 클릭하여 인스턴스 보안그룹으로 이동합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/8.png)

아래 사진과 같이 EC2 인스턴스를 생성할 때 설정한 보안 그룹의 내용을 확인할 수 있습니다. 이제 편집 버튼을 눌러서 수정하겠습니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/9.png)

HTTPS 프로토콜을 삭제하고 사용자 지정 TCP 규칙을 추가 후 포트 범위로는 8080을 입력합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/10.png)

본격적으로 젠킨스를 설치하고 환경을 설정하겠습니다.

윈도우10에서 cmd 창을 킨 다음 my-key-pair.pem이 있는 폴더로 이동합니다.

```
C:\Users\USER>cd C:\Users\USER\github\project\devrami-blog\aws
```

개인키 my-key-pair.pem을 가지고 인스턴스에 접속하겠습니다.

```
ssh -i my-key-pair.pem ec2-user@퍼블릭DNS
```

**Are you sure you want to continue connecting
(yes/no)?** 가 뜨면 yes를 입력한 후 엔터를 누릅니다.

아래와 같은 문구를 보게되면 인스턴스에 성공적으로 접속된 것입니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/images/2020-02-24-img/11.png)

다음과 같이 커맨드를 입력합니다.

```
sudo yum update –y
```

> - Yum은 RPM 기반의 시스템을 위한 자동 업데이트 겸 패키지 설치/제거 도구
> - RPM은 원래 레드햇에서 사용되었던 패키지 파일이었지만 현재는 많은 RPM 기반 배포판(RPM 패키지, RPM 패키지 관리 도구)이 사용되고 있습니다. 즉, RPM을 사용하면 각종 소프트웨어의 설치 및 업데이트를 굉장히 편하게 할 수 있습니다.

AWS는 기본적으로 OS 설치시 타임존이 UTC로 맞춰져있어서 한국 시간에 맞추기위해 다음 작업을 추가로 하겠습니다.

```
# 날짜확인
date

sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime

```

yum이 젠킨스 설치 위치를 알 수 있도록 젠킨스 레파지토리를 추가합니다.

```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
```

패키지를 설치할 수 있게 젠킨스 키 파일을 rpm에 추가한다.

```
 sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
```

> rpm 포맷은 전자서명을 첨부하여 위변조 여부를 확인할 수 있습니다. rpm 패키지가 위변조 되면 에러가 발생합니다. 다른 제조사가 서명했는데 서명자의 검증용 키가 없을 경우도 검증이 실패하게 되는 데 이럴 경우 --import 옵션을 이용하여 검증용 키를 rpm에 추가하면 됩니다.

젠킨스를 설치하겠습니다.

```
sudo yum install jenkins -y
```

젠킨스 서버를 시작합니다.

```
 sudo service jenkins start
```

> 다음과 같은 에러가 발생하면 자바 버전 문제로 자바8로 재 설치한 후 다시 서버를 시작하시면 됩니다.  
> **Starting jenkins (via systemctl): Job for jenkins.service failed because the control process exited with error code. See "systemctl status jenkins.service" and "journalctl -xe" for details.**
>
> ```
> sudo yum remove java-1.7.0-openjdk
> sudo yum install java-1.8.0
> ```

# Jenkins 설정하기

브라우저에서 http://퍼블릭DNS:8080 로 접속하면 아래와 같은 화면이 보일 것입니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/12.png)

근데 매번 8080 포트 입력하기 귀찮죠? Nginx를 프록시로 사용해서 80포트로 들어오면 8080으로 연결되게 추가 설정을 진행하도록 하겠습니다.

우선 Nginx를 설치해줍니다. nginx는 yum으로 설치할 수 없어서 아래처럼 설치해줍니다.

```
sudo amazon-linux-extras install nginx1
```

nginx 서비스를 시작합니다.

```
sudo service nginx start
```

이제 포트없이 퍼블릭 도메인으로 들어가보면 아래 화면처럼 보일 것입니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/17.png)

자! 그럼 젠킨스 서버로 프록시하기위해 추가 설정을 하도록하겠습니다.

nginx 설정 파일에서 location에 proxy_pass 정보를 추가하겠습니다.

```
sudo vi /etc/nginx/nginx.conf
```

다음과 같이 입력하면됩니다.
![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/18.png)

저장 후 nginx 서비스를 재시작하면 처음에 8080포트로 들어갔을 때 봤던 화면이 포트없이 접속해도 잘 보이는 것을 확인할 수 있을 것입니다.

```
sudo service nginx restart
```

이제 젠킨스 내부에서 설정을 해보겠습니다.

접속을 위해 **/var/lib/jenkins/secrets/initialAdminPassword** 에 있는 패스워드를 찾습니다.

```
 sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

위 커맨드로 나오는 내용을 복사하여 Administrator password에 입력한 후 continue 버튼을 클릭합니다.

Customize Jenkins 페이지에서 **Install suggested plugins** 를 선택합니다. 플러그인 설치가 완료되면 Create First Admin User 페이지에서 관리자 계정을 생성합니다. 여기까지 완료했으면 이 페이지를 볼 수 있을 것입니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/13.png)

[AWS 연습하자 2탄 - Jenkins와 Github 연동하기](/post/2020-02-25-how-to-use-aws) 로 이어집니다.
