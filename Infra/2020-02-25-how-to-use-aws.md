# AWS 연습하자 2탄 - Jenkins와 Github 연동

- Title : AWS 연습하자 2탄 - Jenkins와 Github 연동
- Date : 2020-02-25
- Category: Infra

> AWS 연습하자 시리즈
>
> - [AWS 연습하자 1탄 - AWS EC2 인스턴스에 Jenkins 서버 구축하기](/post/2020-02-24-how-to-use-aws)

AWS 연습하기 2탄에서는 추가 설정과 Jenkins와 Github을 연동하는 과정을 다뤄보겠습니다.

# Git 설치

연동에 앞서 Jenkins 서버에 git을 설치해두겠습니다.

```
sudo yum install git
```

# SSH 키 생성 및 등록

젠킨스와 깃허브를 연동하는 작업은 [기억보다 기록을 - Jenkins로 Beanstalk + Multi Module 배포하기 - Jenkins와 Github 연동하기](https://jojoldu.tistory.com/291?category=777282)를 99% 참고하여 진행했습니다.

ssh 키를 생성합니다.

```
sudo ssh-keygen -t rsa -f id_rsa
```

아래 커맨드를 이용해 id_rsa 내용을 확인한 후 복사해둡니다.

```
sudo cat id_rsa
```

그 다음 다시 젠킨스 페이지로 돌아와서 **Credentials -> System -> Global credentials -> Add Credentials**를 선택합니다.

아래와 같이 설정한 후 저장합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/19.png)

이제 공개키(id_rsa.pub)를 Github에 등록하겠습니다.
Jenkins로 관리할 Github 프로젝트로 이동한 뒤 **Settings탭 -> Deploy keys -> Add deploy key** 를 차례로 클릭합니다.

Title에는 Jenkins 입력, key에는 아래 커맨드를 실행한 결과 값을 붙여넣습니다.

```
sudo cat id_rsa.pub
```

Allow write access는 체크 해제한 후 Add Key를 클릭합니다.

이 작업만 해도 Build, Test, Code Clone 등을 다 할 수 있긴 합니다. PUSH 발생시에도 젠킨스가 PUSH 이벤트를 받을 수 있도록 Webhooks를 추가하겠습니다.

**Settings 탭 -> Webhooks -> Add webhook** 을 클릭합니다.

Payload URL에 http://Jenkins도메인/github-webhook/ 을 입력하고 Content type은 **application/json** 으로 변경해줍니다. Add webhook을 클릭하여 추가를 완료합니다.

# Nodejs 설정

제가 연결할 프로젝트는 express 프레임워크가 적용된 nodejs 서버입니다. 우선 Node.js 툴을 설치해줘야합니다. **메인페이지 -> Jenkins 관리 -> 플러그인 관리 -> 설치가능 탭** 을 클릭한 후 검색 창에 **nodejs** 라고 입력합니다. 리스트에서 NodeJS Plugin이 보이면 체크박스 선택 후 재시작없이 설치하기를 클릭합니다. 설치가 완료되면 다시 메인 페이지로 돌아갑니다.

이제 Node.js 툴 설정을 진행하겠습니다. **Jenkins 관리 -> Global Tool Confituration** 을 선택합니다.

**NodeJS 섹션 -> NodeJS installations..** 를 클릭합니다.

아래와 같이 입력한 후 저장합니다.
저는 로컬에서 노드 버전이 12.14.1 여서 다음과 같이 선택했습니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/16.png)

# Item 생성 및 파이프라인 작성

**새로운 Item -> 적당한 이름 입력 -> Pipeline** 선택 후 OK를 눌러줍니다.

Build Triggers가 Github hook과 연동되도록 다음과 같이 선택합니다.

![process tree](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-02-24-img/20.png)

Pipeline 섹션에 Definition은 Pipeline script로 한 후 아래와 같이 입력하고 저장합니다. Pipeline 스크립트를 jenkinsfile로 관리하는 것은 뒤에서 다루도록 하겠습니다.

```
pipeline {
   agent any

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
              echo 'upload s3'
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

생성된 아이템으로 이동한 후 Build Now 버튼을 눌렀을 때 에러없이 완료되면 Github 연동은 성공적으로 된 것입니다.

[AWS 연습하자 3탄 - Jenkins와 S3 버킷 & AWS codeDeploy 연동으로 배포하기](/post/2020-02-26-how-to-use-aws) 으로 이어집니다.

[참고 블로그](https://medium.com/@gustavo.guss/jenkins-starting-with-pipeline-doing-a-node-js-test-72c6057b67d4)
