# Vue CLI 없이 Vue, Typescript, Babel, Webpack 설정하기 1탄

- 프론트
- 2020년 02월 29일

## Why?

Vue CLI로 프로젝트를 생성해왔는데 문뜩 웹팩과 바벨에 대한 이해가 부족한듯 싶었다. 그래서😎 오늘은 Vue, Typescript, Babel, Webpack을 사용해서 Vue CLI 없이 프로젝트를 설정해보면서 공부한 내용을 정리해보기로 했다. 1탄은 웹팩과 바벨에 대한 설정과 개념을 잡아보고 2탄에서 Vue와 Typescript 설정을 진행할 거다.

## 1. 프로젝트 생성

    $ mkdir vue-typescript
    $ cd vue-typescript
    $ mkdir src
    $ cd src
    $ mkdir components
    $ cd ..

## 2. package.json 파일 생성

> **npm(node package manager)** 은 자바스크립트 패키지 매니저다. Node.js에서 사용할 수 있는 모듈들을 패키지화하여 모아둔 저장소 역할과 패키지 설치 및 관리를 위한 CLI(Common line interface)를 제공한다.  비슷한 패키지 매니저인 페이스북이 만든 **yarn**은 npm보다 더 빠르고 안전하다. 둘 다 package.json 파일에 선언된 semver rule을 만족하는 최신 버전을 설치한다.

    $ npm init

**package.json란?**

- 프로젝트가 의존하고 있는 패키지들의 리스트를 보여준다.
- semantic versioning rule에 따라 프로젝트에 의존한 패키지들의 버전을 설정할 수 있다.
- 빌드를 재현할 수 있어 좀 더 쉽게 다른 개발자와 공유할 수 있다.

> **—save-dev** 란? —production 빌드 시 포함될 필요가 없는 패키지는 —save-dev 옵션을 사용하여 package.json의 devDependencies에 패키지 정보를 저장한다.

## 3. Webpack 설치 및 초기 설정

> 웹팩은 자바스크립트 애플리케이션을 위한 **정적 모듈 번들러**다. 자바스크립트 코드를 하나의 파일로 관리하는 것과 여러개의 파일로 관리하는 것은 각각 단점이 있다. 우선 각 파일을 브라우저에 로딩하면 그만큼 네트워크 비용이 커진다. 또한 각 파일이 서로의 스코프를 침범하여 변수 충돌의 위험이 생길 수도 있다. 물론 해결 방법이 있다. 자바스크립트는 함수 스코프를 사용하니 즉시호출함수를 사용해 모듈을 만들면 된다. 그러나 모듈을 모두 즉시호출함수로 변경하여 하나의 파일로 묶는 것을 일일이 작업하면 귀찮지않을까? 웹팩이 등장한 배경이 바로 여기에 있다.

    npm install --save-dev webpack webpack-cli webpack-dev-server html-webpack-plugin

- webpack : 웹팩
- webpack-cli : 커맨드 라인에서 웹팩을 구동시키기위한 패키지
- webpack-dev-server : 실시간 리로드를 제공하는 개발 서버로 localhost에서 테스트하고 싶을 때 사용
- html-webpack-plugin : 웹팩 번들을 서비스하기 위한 HTML 파일을 만들어주는 패키지로 컴파일할 때마다 이름이 변하는 번들파일을 HTML 파일에 넣어줄 때 유용하다.

설치가 완료되면 src 폴더에 index.js 파일을 만들고 package.json 파일의 scripts에 **"build": "webpack"** 와 **"start:dev": "webpack-dev-server"** 를 추가한다. 

    {
      "name": "vue-typescript",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
    		"start:dev": "webpack-dev-server",
    		"build": "webpack",
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "html-webpack-plugin": "^3.2.0",
        "webpack": "^4.42.0",
        "webpack-cli": "^3.3.11",
        "webpack-dev-server": "^3.10.3"
      }
    }

웹팩4는 webpack.config.js 파일이 없어도 파일을 번들할 수 있다. 커맨드 라인에서 **npm run build** 를 치면 dist 폴더에 번들된 파일인 main.js를 볼 수 있다. 또한 **npm run start:dev** 로 치면 8080포트로 개발서버가 동작한다. 하지만 index.html 파일이 없어서 localhost:8080에서 제대로 된 화면을 볼 수 없다. index.html 파일을 직접 dist 폴더에 만들어서 main.js 파일을 **<script src="main.js"></script>** 로 로딩시켜서 동작시킬 수는 있다. 하지만 번들 파일의 이름이 매번 바뀐다면? 또 번들 파일이 많다면? 매우 귀찮을 거다. 그래서 **html-webpack-plugin** 을 이용하여 자동으로 index.html 파일을 생성시키고 번들 파일들을 주입시킬 거다. 설정을 위해 프로젝트 루트에 프로젝트 루트에 webpack.config.js 파일을 생성한 후 아래와 같이 작성한다. 

    const path = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './src/index.js',
      plugins: [
        new HtmlWebpackPlugin()
      ],
      output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist'),
      },
    };

- **entry** : 자바스크립트가 로딩하는 모듈이 많아질수록 모듈간의 의존성은 증가한다. 웹팩에서 의존성 그래프의 시작점을 엔트리(entry)라고 한다.
- **output** : entry에 설정한 main.ts을 시작으로 의존되어있는 모든 모듈을 하나로 묶은(번들된) 결과의 위치를 output에 정의한다. webpack을 실행하면 /dist 폴더에 main.bundle.js란 이름으로 파일이 생성될 것이다.

**npm run build** 를 실행시키면 dist 폴더에 index.html 파일이 생성되고  <script type="text/javascript" src="main.js">로 번들 파일이 주입된다. 

webpack-dev-server 에 대한 추가적인 설정을 하려면 devServer 옵션을 이용하면 된다.

    module.exports = {
      devServer: {
        hot: true,
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 9000
      },
    };

hot: true 는 핫로더, contentBase는 대상 위치,  compress는 파일 gzip 압축, port는서버 포트 설정이다. 

[DevServer | webpack](https://webpack.js.org/configuration/dev-server/)

    DevTools failed to parse SourceMap: webpack:///node_modules/sockjs-client/dist/sockjs.js.map

[localhost:9000](http://localhost:9000) 에서 콘솔 창을 확인하면 devtools을 설정하지 않아 위와 같은 경고 문구가 뜬다. devtools 옵션으로 소스맵 생성을 정의할 수 있다.  소스맵은 번들링된 파일과 원래 파일을 맵핑하여 에러 추적할 때 용이하다. 예를 들어 세개의 js 파일을 번들링한 main.js 파일이 있다고 하자. 콘솔 창을 확인해보니 main.js 파일에서 에러가 났다. 만약 소스맵을 사용하지않는다면 세 개의 파일 중 어디서 에러가 났는 지 알 수가 없다. *여러가지 종류가 있지만 inline-source-map 을 추천한다길래 얘를 사용했다. 나중에 각 옵션의 차이점도 알아봐야겠다.*

    module.exports = {
      devtool: 'inline-source-map'
    };

## 4. Babel로 ES6를 ES5로 변환하기

> 구형 브라우저(IE)에서는 ES6 코드가 호환되지 않는다. 그렇다고 ES5로 코드를 짤 수도 없는 노릇.  ES2015+ 문법을 ES5로 바꿔주는 녀석이 없을까? 바로 바벨이 그 역할을 한다.

먼저 바벨 패키지를 설치한다.

    npm install --save-dev babel-loader @babel/core @babel/preset-env

- babel-loader : 웹팩에서 ES6로 된 파일을 ES5로 변환할 때 사용
- @babel/core

웹팩에게 빌드하기전 자바스크립트 파일을 ES5로 트랜스파일링하라고 말해야한다. 웹팩의 Loaders를 이용하여 이를 설정할 수 있다.

    module.exports = {
      module: {
        rules: [
          { 
            test: /\.js$/, 
            exclude: /node_modules/, 
            loader: "babel-loader" 
          }
        ]
      }
    };

- **loader** : 웹팩은 자바스크립트, 이미지, 폰트, 스타일시트도 전부 모듈로 관리한다. 하지만 웹팩은 자바스크립트만 아는 녀석이라서 웹팩이 자바스크립트 파일이 아닌 파일들을 이해할 수 있게끔 변경해야하는데 로더(loader)가 그런 역할을 한다. 위 코드를 보면 test에 로딩할 파일, use에 적용할 로더, exclude는 제외할 파일를정의한 것을 알 수 있다.

바벨 설정을 완료했지만 사실 바벨은 아무 것도 하지않는다. 프로젝트 루트에 .babelrc 파일을 만들어 설정을 해야한다. 그 전에 ES2015+를 트랜스파일링하기위해 필요한 프리셋과 플러그인을 모은 모듈을 다운받는다. @babel/preset-env는 자동으로 브라우저 polyfill 을 맞춘다.

    npm install @babel/preset-env --save-dev

.babelrc에 아래와 같이 입력한다. 

    {
      "presets": ["@babel/preset-env"]
    }

따로 .babelrc 파일을 만들지않고 웹팩에서도 설정 가능하다.

    module.exports = {
      module: {
        rules: [
          { 
            test: /\.js$/, 
            exclude: /node_modules/, 
    				use: {
    					loader: "babel-loader",
    					options: {
    	          presets: [
    	            '@babel/preset-env',
                ]
              }
    				} 
          }
        ]
      }
    };

실제로 트랜스파일링되는지 테스트하기위해 index.js에 아래와 같이 작성하고 npm run build하면 es5 문법으로 바뀐 것을 알 수 있다.

    const myconst = 123;
    let mylet = 456;

![Vue%20CLI%20Vue%20Typescript%20Babel%20Webpack%201/Untitled.png](Vue%20CLI%20Vue%20Typescript%20Babel%20Webpack%201/Untitled.png)

추가로 @babel/preset-env에서 타켓에 옵션으로 브라우저 버전을 직접 설정할 수도 있다. options을 쓰거나 package.json에 작성하는 방법도 있지만 공식문서는 **.browserslistrc** 파일을 만들어서 설정하는 것을 추천하고 있다. 프로젝트 루트 폴더에 .browserslistrc를 만든 후 아래의 내용을 작성하자. 각 브라우저에서 최근 2개의 버전까지, 1% 이상 사용되는 브라우저를 타켓으로 한다.  디폴트는 *> 0.5%, last 2 versions, Firefox ESR, not dead* 다.

    > 1%
    last 2 versions

### ES6 메서드나 생성자 지원하려면?

index.js 에 console.log(Array.from('foo')); 을 입력한 후 IE11에서 확인해보자.

    console.log(Array.from('foo'));

콘솔 창을 보면 개체가 'from' 속성이나 메서드를 지원하지 않습니다. 라는 에러 문구를 보게될 것이다. 바벨로 ES5로 바꿨는 데 왜 그러지??? 라는 혼란이 생길 것이다.😨😨

방금까지는 바벨을 이용하여 ES6 문법을 ES5로 문법 트랜스파일링만 한 거다.  그래서 Array.from()과 같이 ES6에만 있는 메서드나 생성자들은 구형 브라우저에서 동작하지 않는다. 이 문제를 해결하기 위해polyfill을 사용한다. **polyfill**은 code 조각으로 런타임에 존재하지 않는 nativeAPI의 복사본을 말한다. @babel/polyfill이나 @babel/plugin-transform-runtime를 사용하면 되지만 **@babel/polyfill** 은 7.4.0부터 deprecated 되서 **core-js@3**를 사용하는 것을 추천한다.

**Babel-Polyfill vs Babel-Transform-Runtime**

- Babel-Polyfill은 전역적으로 polyfill을 사용하여 전역 스코프를 오염시킬 수 있다. 또한 번들 파일에 포함되고 코드 상단에 **import 'babel-polyfill';** 을 추가해야 사용할 수 있다. 프로젝트에서 전역적으로 polyfill을 사용하거나 라이브러리 규모가 클 경우에 사용하는 게 유리하다.
- Babel-Transform-Runtime는 플러그인으로 추가하면 되고 기능은babel-polyfill과 거의 비슷하나 array.includes()와 array.values() 같은 특정 함수를 제공하지않는다. core-js@3과 함께 사용하면 이 문제를 해결할 수 있다. 간단하게 라이브러리에서 사용하는 API를 래핑시킬 때 좋다.

여기서는 core-js@3를 사용하며 @babel/preset-env의 옵션으로 폴리필을 설정한다. 우선 core-js를 설치해주자.

    npm install core-js@3 --save

.babelrc에 아래와 같이 추가한다. "useBuiltIns": "usage"는 트랜스파일 시 소스에서 쓰는 폴리필을 자동으로 넣어준다.

    {
      "presets": [
        [
          "@babel/preset-env",
          {
            "useBuiltIns": "usage",
            "corejs": { 
              "version": 3, 
              "proposals": true 
            }
          }
        ]
      ]
    }

구글 크롬 브라우저에 들어가보면 아래와 같이 트랜스 파일링 된 것을 알 수 있다.

    /***/ "./src/index.js":
    /*!**********************!*\
      !*** ./src/index.js ***!
      \**********************/
    /*! no exports provided */
    /***/ (function(module, __webpack_exports__, __webpack_require__) {
    
    "use strict";
    __webpack_require__.r(__webpack_exports__);
    /* harmony import */ var core_js_modules_es_array_from__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! core-js/modules/es.array.from */ "./node_modules/core-js/modules/es.array.from.js");
    /* harmony import */ var core_js_modules_es_array_from__WEBPACK_IMPORTED_MODULE_0___default = /*#__PURE__*/__webpack_require__.n(core_js_modules_es_array_from__WEBPACK_IMPORTED_MODULE_0__);
    /* harmony import */ var core_js_modules_es_string_iterator__WEBPACK_IMPORTED_MODULE_1__ = __webpack_require__(/*! core-js/modules/es.string.iterator */ "./node_modules/core-js/modules/es.string.iterator.js");
    /* harmony import */ var core_js_modules_es_string_iterator__WEBPACK_IMPORTED_MODULE_1___default = /*#__PURE__*/__webpack_require__.n(core_js_modules_es_string_iterator__WEBPACK_IMPORTED_MODULE_1__);
    
    
    console.log(Array.from('foo'));
    
    /***/ }),

IE 11에서도 폴리필하려면 브라우저 타켓에 IE 11을 추가해야한다. .browserslistrc에 아래와 같이 추가하고 IE 브라우저를 확인하면 잘 동작하는 것을 볼 수 있다.

    > 1%
    last 2 versions
    IE 11

**참고**

[웹팩의 기본 개념](http://jeonghwan-kim.github.io/js/2017/05/15/webpack.html)

[[Tool] (번역)Babel에 대한 모든 것](https://jbee.io/etc/Everything-about-babel/)