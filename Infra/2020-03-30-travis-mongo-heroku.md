# Travis에서 MongoDB 사용하기 & Heroku 배포하기

- Title : Travis에서 MongoDB 사용하기
- Date : 2020-03-30
- Category : Infra

# WHY?

Travis에서 MongoDB Altas 연결이 실패하여 테스트 실패하는 현상이 계속 발생하였다. 로컬에서는 잘되는 데 왜 travis에서는 안될까? 해결 방법은 간단했다. travis 내에서 로컬 MongoDB를 돌려주는 거였고 이는 Travis가 아주 편리하게 제공하고 있었다.

# SOLVE

1. .travis.yml 에 아래 코드를 추가해준다.

    services:
      – mongodb

2. travis → setting → 변수 설정에서 이름은 각자 코드 내에서 쓰는 환경 변수 이름을 적고 값은 `mongodb://localhost:27017/mydb_test` 로 하면 된다.

travis에서 mongodb를 사용할 때는 따로 데이터베이스 만들 필요가 없고 로컬 호스트(127.0.0.1)로 바인딩되므로 위와 같이 쓰면 된다.

# WHY?

헤로쿠로 자동 배포되게 하는 거 까먹어서 기록 + Node.js 앱 배포

[Travis CI Documentation](https://docs.travis-ci.com/user/deployment/heroku/)

# SOLVE

1. .travis.yml에 아래와 같이 작성한다.

    deploy:
      provider: heroku
      api_key:
        secure: <암호화한 heroku api token>
      app: express-project-blog

- app은 헤로쿠 앱 이름을 적으면 된다. 안 적어도 되긴 함

2. secure 키 구하기

- Heroku CI를 깔고 token 얻기. 아래 명령어를 치면 나오는 토큰을 복사해놓는다.

    heroku auth:token

- 윈도우인 경우 ruby cmd창에 travis CI를 깔고 아래 명령어 입력. 이 때 명령어 실행 위치에 .travis.yml 파일이 있어야한다. 따로 repo 설정을 안해줘서 —repo 옵션으로 github 위치 적어주면 travis.yml 파일에 알아서 암호화된 api_key가 생김

    travis encrypt <복사한 TOKEN> --add deploy.api_key --pro --repo=devgaram/express-mongo-rest-api

> [travis-ci.com](http://travis-ci.com/) 에서 빌드할 경우 —pro 옵션과 함께 암호화해야 정상적으로 인증할 수 있음..

3. 헤로쿠 설정

- 빌드팩에 nodejs 추가하기

![Travis%20MongoDB%20Heroku/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Infra/images/2020-03-30-img/Untitled.png)

- 프로젝트에서 es6를 사용하기 위해 babel을 이용해 빌드하는 경우가 있는 데, 이 때는 환경변수를 따로 설정해줘야 devdependencies도 설치되서 babel을 실행할 수 가 있다.

![Travis%20MongoDB%20Heroku/Untitled%201.png](Travis%20MongoDB%20Heroku/Untitled%201.png)