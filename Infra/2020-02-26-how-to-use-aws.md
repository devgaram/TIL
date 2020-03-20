# AWS 연습하자 3탄 - Jenkins와 S3 버킷 & AWS codeDeploy 연동으로 배포하기

- Title : AWS 연습하자 3탄 - Jenkins와 S3 버킷 & AWS codeDeploy 연동으로 배포하기
- Date : 2020-02-26
- Category: Infra

> AWS 연습하자 시리즈
>
> - [AWS 연습하자 1탄 - AWS EC2 인스턴스에 Jenkins 서버 구축하기](/post/2020-02-24-how-to-use-aws)
> - [AWS 연습하자 2탄 - Jenkins와 Github 연동](/post/2020-02-25-how-to-use-aws)

AWS 연습하기 3탄에서는 AWS S3와 Aws Codedeploy로 자동 배포 환경을 구축하는 과정을 다루겠습니다.

# 배포 서버인 EC2 인스턴스 생성

[AWS 연습하자 1탄](/post/2020-02-24-how-to-use-aws)의 AWS EC2 인스턴스 생성하기 부분을 진행하여 인스턴스를 생성하고 오세요! 저는 Name 태그에 blog-server로 인스턴스를 생성했습니다.

# AWS Code Deploy 계정 생성

여기서 생성한 계정을 가지고 Jenkins와 blog-server 인스턴스에서 설정을 진행할 것입니다.

**[AWS IAM 콘솔](https://console.aws.amazon.com/iam/) -> 사용자 탭 -> 사용자 추가** 를 클릭합니다.

사용자 이름을 입력하고 액세스 유형은 프로그래밍 방식 액세스를 선택합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/29.png)

해당 계정이 사용할 수 있는 정책으로는 CodeDeploy와 S3 권한을 할당 받겠습니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/30.png)

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/31.png)

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/32.png)

.csv 다운로드 버튼을 클릭하여 비밀키를 잘 보관해둡니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/33.png)

# AWS S3 버킷 생성

**[AWS S3 콘솔](https://s3.console.aws.amazon.com/s3/) -> 버킷 만들기** 를 클릭합니다.

버킷 이름이 blog-server-bucket으로 하고 리전이 서울인지 확인합니다. 추가 옵션 없이 다음을 계속하여 버킷 생성을 완료합니다.

# IAM Role 생성

blog-server EC2와 CodeDeploy에게 신뢰할 수 있는 권한을 설정하여 젠킨스가 정상적으로 배포할 수 있게 하겠습니다.

> CodeDeploy가 EC2 접근할 수 있도록 설정하는 것!

**[AWS IAM 콘솔](https://console.aws.amazon.com/iam/) -> 역할 -> 역할 만들기** 를 클릭합니다.

AWS 서비스를 누른 후 이 역할을 사용할 서비스 선택에서 **EC2**를 선택합니다. 권한 정책으로 **AmazonEC2RoleforAWSCodeDeploy** 를 체크한 후 다음: 태그로 넘어갑니다. 태그는 건너 뛰고 역할 이름은 **blog-server-EC2CodeDeployRole**을 입력한 후 역할 만들기를 클릭하겠습니다.

지금 만든 역할은 blog-server EC2에 IAM 역할로 설정할 것입니다.

마찬가지로 CodeDeploy도 역할을 생성하겠습니다.

**[AWS IAM 콘솔](https://console.aws.amazon.com/iam/) -> 역할 -> 역할 만들기** 를 클릭합니다.

AWS 서비스를 누른 후 이 역할을 사용할 서비스 선택에서 **CodeDeploy**를 선택합니다. 사용 사례 선택 섹션에서도 **CodeDeploy**를 선택합니다. 권한 정책으로 **AWSCodeDeployRole** 를 하나이므로 그냥 확인하고 다음: 태그로 넘어갑니다. 태그는 건너 뛰고 역할 이름은 **blog-server-CodeDeployServiceRole**을 입력한 후 역할 만들기를 클릭하겠습니다.

# EC2에 AWS 역할 적용하기

EC2 콘솔로 이동한 후 아래와 같이 IAM 역할 연결/바꾸기 를 선택합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/34.png)

아까만든 **blog-server-EC2CodeDeployRole** 을 선택한 후 적용합니다.

# EC2 AWS CodeDeploy Agent 설치 및 설정

이제 blog-server 인스턴스에 CodeDeploy Agent를 설치하겠습니다.

인스턴스로 접속한 후 패키지 업데이트를 수행합니다.

```
ssh -i my-key-pair.pem ec2-user@퍼블릭DNS
sudo yum update -y
```

aws cli를 설치하겠습니다.

```
sudo yum install awscli
```

에이전트 설치 후 aws 설정을 하겠습니다.

```
sudo aws configure
```

AWS Access Key ID, AWS Secret Acecess Key ID는 사용자 생성할 때 받은 CSV를 보고 입력합니다.

추가 정보는 아래와 같이 입력 후 엔터칩니다.
Default region name: ap-northeast-2
Default output format: json

계속 설치를 진행하겠습니다.

```
cd /home/ec2-user

# agent 파일 다운로드
aws s3 cp s3://aws-codedeploy-ap-northeast-2/latest/install . --region ap-northeast-2

# 실행권한 추가
chmod +x ./install

# 설치 진행
sudo ./install auto

# agent가 실행 중인지 확인 PID가 나오면 정상적으로 실행 중인 상태입니다.
sudo service codedeploy-agent status
```

만약 **sudo ./install auto** 커맨드 실행 결과 **/usr/bin/env: ruby: No such file or directory**가 나온다면 루비를 설치해야합니다. 아래와 같이 실행 후 다시 설치를 진행합니다.

```
sudo yum install ruby -y
```

추가로 재 부팅시 자동으로 code deploy agent가 실행될 수 있도록 스크립트를 생성하고 권한을 주겠습니다.

```
# 아래 스크립트를 입력합니다.
sudo vim /etc/init.d/codedeploy-startup.sh

sudo chmod +x /etc/init.d/codedeploy-startup.sh
```

> #!/bin/bash  
> echo 'Starting codedeploy-agent'  
> sudo service codedeploy-agent restart

# nginx와 Docker로 무중단 배포하기

## 1) 도커, 도커컴포즈 설치 및 프로젝트 파일 생성

도커 컨테이너 위에서 애플리케이션을 구동하기위해서 ec2에 도커와 도커컴포즈를 설치합니다.

```
sudo yum install docker

# 도커 컴포즈 다운로드
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 실행 권한 주기
$ sudo chmod +x /usr/local/bin/docker-compose

# 설치 완료됐는 지 확인하기
$ docker-compose --version

# bash: docker-compose: command not found 문구가 나오면 아래 커맨드 실행하기
$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

잠시 배포에 대해 짚고 넘어가겠습니다. 뒤에서 실습을 진행하겠지만, AWS Code Deploy에서 배포한 파일은 /home/ec2-user/build 에 복사되게 할 것입니다.

배포 후 /home/ec2-user/build 이 폴더에는 Dockerfile 과 docker-compose.yml 파일이 존재할것이고 이를 이용해 컨테이너에 서버를 올릴 것입니다.

혹시 docker ps 해봤더니 **Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?** 에러가 떴다면 docker service가 실행이 안된 것이므로 아래와 같이 명령을 내리겠습니다. [도커 설치 가이드](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)를 참고했습니다.

```
sudo groupadd docker
sudo usermod -aG docker $USER
sudo newgrp docker

# 아래 커맨드 실행 결과 상태가 active여야 정상적인 것!
sudo systemctl status docker
sudo systemctl start docker
sudo systemctl enable docker
```

이제 프로젝트에 Dockerfile과 docker-compose 파일을 작성하겠습니다.

> 도커에 대해 기본지식이 필요합니다. 나중에 도커 관련 포스팅도 작성하겠습니다..!

프로젝트 루트에 다음과 같이 Dockerfile을 작성합니다.

```
# 로컬과 같은 버전
FROM node:12.14

# 도커 이미지 만든 사람
LABEL maintainer="joingaram@gmail.com"

# 3000포트로 도커 데몬에 연결
EXPOSE 3000

# 작업 디렉토리 & 자동으로 작업 디렉토리로 현재 위치 변경
WORKDIR /usr/src/app

COPY package.json .
COPY yarn.lock .
RUN yarn cache clean & yarn install --network-timeout 100000
COPY . .

CMD ["yarn", "start"]

```

프로젝트 루트에 docker-compose.blue.yml 파일과 docker-compose.green.yml 파일을 생성한 후 아래와 같이 입력합니다. 포트만 3001, 3002로 다릅니다.

docker-compose.blue.yml

```
version: "3.7"
services:
  blog-server:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - .:/usr/src/app
    ports:
      - "3001:3000"
```

docker-compose.green.yml

```
version: "3.7"
services:
  blog-server:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - .:/usr/src/app
    ports:
      - "3002:3000"
```

## 2) nginx로 로드밸런싱 설정

우선 nginx를 설치합니다. 프록시 서버로 두 개의 도커 컨테이너를 로드밸런싱하는 역할을 담당할 것입니다.

```
sudo amazon-linux-extras install nginx1
sudo service nginx start

# 잘 실행되었는지 확인
ps -ef | grep nginx
```

이제 **nginx 설정 파일 수정** 작업을 하겠습니다.

```
sudo vi /etc/nginx/nginx.conf
```

다음과 같이 수정한 후 저장합니다.

```
# Load Balancing
upstream blog-server {
  least_conn;
  server 127.0.0.1:3001 weight=5 max_fails=3 fail_timeout=10s;
  server 127.0.0.1:3002 weight=10 max_fails=3 fail_timeout=10s;
}

server {
  listen 80;
  server_name 서버 아이피; # 세미콜론 붙여주셔야 합니다.
  location / {
    proxy_pass http://blog-server;
  }
}
```

잘 설정했는 지 확인하고 nginx를 재시작합니다.

```

sudo nginx -t
sudo service nginx restart
```

> [생활코딩nginx](https://opentutorials.org/module/384/4328)
>
> - Nginx는 4개의 로드밸런싱 메서드를 제공합니다. 그중 least_conn 은 연결이 가장 작은 서버로 요청을 보냅니다.
> - weight=n : 업스트림 서버의 비중을 나타냅니다. 이 값을 2로 설정하면 그렇지 않은 서버에 비해 두배 더 자주 선택됩니다.
> - max_fails=n : n으로 지정한 횟수만큼 실패가 일어나면 서버가 죽은 것으로 간주합니다.
> - fail_timeout=n : max_fails가 지정된 상태에서 이 값이 설정만큼 서버가 응답하지 않으면 죽은 것으로 간주합니다.

# Jenkins 배포 설정

우선 Pipeline AWS STEP과 AWS Codedeploy 플러그인을 설치해줍니다. Pipeline AWS STEP은 S3로 소스 전송할 때, AWS Codedeploy로는 S3 버킷의 코드를 인스턴스에 배포하도록 설정하겠습니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/22.png)

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/25.png)

각 플러그인 사용방법

- [Pipeline AWS STEP](https://github.com/jenkinsci/pipeline-aws-plugin#deployapi)
- [AWS Codedeploy](https://github.com/jenkinsci/aws-codedeploy-plugin)

## 1) S3 UPLOAD 작성

먼저 AWS 접근을 위한 설정을 하겠습니다.

**젠킨스 메인 -> Credentials -> System -> Global credentials -> Add Credentials** 를 차례로 클릭합니다.

Kind는 AWS Credentials를 선택하고 Access key와 secret key는 위에서 생성한 csv 파일을 보고 입력합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/23.png)

OK 클릭 후 클릭해보면 아래와 같이 ID를 볼수 있습니다. 파이프라인 작성 시에 사용해야하므로 저장해둡니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/24.png)

AWS 연습하기 2탄에서 작업했던 파이프라인을 아래와 같이 수정합니다.

- credentials에는 위에서 복사한 ID를 입력합니다.
- Bucket에는 아까 생성한 S3 버킷 입력을 입력합니다.

```
pipeline {
   agent any

   environment {
       S3PATH = "${env.JOB_NAME}"
   }
   tools {
      nodejs "node"
   }

   stages {
      stage('Build') {
         steps {
            git 'https://github.com/devgaram/express-project-blog.git'
         }
      }

      stage('Install dependencies') {
          steps {
              sh 'npm install -g yarn'
              sh 'yarn install'
          }
      }
      stage('Test') {
          steps {
              echo 'test..'
              // yarn test
          }
      }
      stage('Upload S3') {
          steps {
              echo 'Upload S3'
              withAWS(credentials: '667cec8d-baa7-497f-b2db-2d424c121a22') {
                s3Upload(file: '.', bucket: 'blog-server-bucket', path: "${S3PATH}", excludePathPattern: '**/node_modules/**, **/.git/**')
              }
          }
      }
      stage('Deploy') {
          steps {
              echo 'deploy'
          }
      }
   }
   post {
        success {
            echo 'successed'
        }
        failure {
            echo 'failed'
        }
   }
}
```

**Apply -> 저장 -> Build Now** 를 클릭하면 잘 실행될 것입니다.

## 2) AWS CodeDeploy 설정

**Jenkins 메인 -> blog-server 아이템 -> Pipeline Syntax -> Snippet Generator**를 선택합니다.

Snippet Generators는 파이프라인 스크립트 생성에 도움을 주는 녀석입니다. 얘를 이용해 AWS CodeDeploy를 이용한 배포를 설정하겠습니다.

**Steps 섹션 -> Sample Step - step:General Build Step 선택 -> Build Step - Deploy an application to AWS CodeDeploy 선택** 을 진행합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/26.png)

- AWS CodeDeploy Application Name: EC2 인스턴스 이름
- AWS CodeDeploy Deployment Group: CodeDeploy 그룹 명
- AWS CodeDeploy Deployment Config: 배포 환경, 여기선 CodeDeployDefault.OneAtATime
- AWS Region: AP_NORTHEAST_2
- S3 Bucket: S3 버킷 이름

Use Access/Secret keys 라디오 버튼을 선택하여 csv로 저장했던 내용을 입력해줍니다.

마지막으로 Generate Pipeline Script 버튼을 클릭하면 나오는 텍스트를 복사합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/27.png)

최종적으로 파이프라인을 아래와 같이 구성됩니다.

```
pipeline {
   agent any

   environment {
       S3PATH = "${env.JOB_NAME}"
       AWS_SECRET_ACCESS_KEY = "${env.AWS_SECRET_ACCESS_KEY}"
   }
   tools {
      nodejs "node"
   }

   stages {
      stage('Build') {
         steps {
            git 'https://github.com/devgaram/express-project-blog.git'
         }
      }

      stage('Install dependencies') {
          steps {
              sh 'npm install -g yarn'
              sh 'yarn install'
          }
      }
      stage('Test') {
          steps {
              echo 'test..'
              // yarn test
          }
      }
      stage('Upload S3') {
          steps {
              echo 'Upload S3'
              withAWS(credentials: '667cec8d-baa7-497f-b2db-2d424c121a22') {
                s3Upload(file: '.', bucket: 'blog-server-bucket', path: "${S3PATH}", excludePathPattern: '**/node_modules/**, **/.git/**')
              }
          }
      }
      stage('Deploy') {
          steps {
              echo 'deploy'
              step([$class: 'AWSCodeDeployPublisher', applicationName: 'blog-server', awsAccessKey: 'AKIASDBC2NNSJWD4F76B', awsSecretKey: "${AWS_SECRET_ACCESS_KEY}", credentials: 'awsAccessKey', deploymentConfig: 'CodeDeployDefault.OneAtATime', deploymentGroupAppspec: false, deploymentGroupName: 'blog-server-CodeDeploy-group', excludes: '', iamRoleArn: '', includes: '**', proxyHost: '', proxyPort: 0, region: 'ap-northeast-2', s3bucket: 'blog-server-bucket', s3prefix: '', subdirectory: '', versionFileName: '', waitForCompletion: false])
          }
      }
   }
   post {
        success {
            echo 'successed'
        }
        failure {
            echo 'failed'
        }
   }
}


```

## 3) AWS Deploy 설정 파일

AWS CodeDeploy는 프로젝트 루트에 있는 appspec.yml를 이용하여 배포를 진행합니다. 자세한 내용은 [aws 가이드](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/reference-appspec-file.html#appspec-reference-server)에서 확인하세요.

프로젝트 루트에 appspec.yml 파일 생성 후 아래와 같이 입력합니다. AWS CodeDeploy가 ec2의 /home/ec2-user/build/ 위치에 S3 버킷에 있는 코드를 옮기도록 설정했습니다. 배포 후에는 execute-deploy.sh 작업을 통해 도커 컨테이너를 올릴 것입니다.

```
version: 0.0
os: linux
files:
  - source:  /
    destination: /home/ec2-user/build/
hooks:
  AfterInstall: # 배포 후
    - location: execute-deploy.sh
      timeout: 180
```

execute-deploy.sh

```
#!/bin/bash
cd /home/ec2-user/build
chmod +x ./deploy.sh
./deploy.sh > /dev/null 2> /dev/null < /dev/null &
```

현재 블루 컨테이너가 돌고 있다면 그린 컨테이너를 구동한 후 블루 컨테이너를 종료합니다. 이 방법을 통해 무중단 배포를 할 수 있는 것입니다. 아래와 같이 작성합니다.

deploy.sh

```
#!/bin/bash

DOCKER_APP_NAME=blog-server

EXIST_BLUE=$(docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml ps | grep Up)

if [ -z "$EXIST_BLUE" ]; then
	echo "blue up"
	docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml up -d

	sleep 10

	docker-compose -p ${DOCKER_APP_NAME}-green -f docker-compose.green.yml down
else
	echo "green up"
	docker-compose -p ${DOCKER_APP_NAME}-green -f docker-compose.green.yml up -d

	sleep 10

	docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml down
fi
```

자 이제 실제로 커밋 푸시하면 배포까지 완료되는 것을 볼 수 있습니다!

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/28.png)

> 추가로 해야할 것
>
> - S3에 왜 node_modules랑 .git도 올라가는 거지..? 분명 제외시켰는 데..

참고

- [기억보단 기록을](https://jojoldu.tistory.com/265)
- https://velog.io/@jeff0720/Travis-CI-AWS-CodeDeploy-Docker-%EB%A1%9C-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94-%EB%B0%8F-%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0-2
- https://medium.com/faun/create-a-continuous-delivery-pipeline-using-jenkins-gitlab-github-and-deploy-on-aws-ec2-with-3aaadf073196
