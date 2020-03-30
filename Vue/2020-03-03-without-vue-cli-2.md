# Vue CLI 없이 Vue, Typescript, Babel, Webpack 설정하기 2탄

- Title : Vue CLI 없이 Vue, Typescript, Babel, Webpack 설정하기 2탄
- Date : 2020-03-03
- Category: Vue

## Why?

에 이어 오늘은 Typescript와 Vue를 설정할거다.

## 5. Vue 설치 및 설정

    npm install --save-dev vue vue-loader vue-template-compiler

> vue 가 릴리즈 될 때 vue-template-compiler도 릴리즈된다. vue-template-compiler 버전이 vue 패키지와 동기되어야 vue-loader가 vue를 웹팩이 이해할 수 있는 코드로 생산할 수 있다. 즉 프로젝트에서 vue를 업그레이드 했으면 반드시 vue-template-compiler 버전도 맞춰줘야한다.

- vue-loader : vue-loader는 rule 설정 외에 추가적으로 vue loader 플러그인을 설정해야한다. 예를 들어 **new VueLoaderPlugin()** 을 해야 .vue 파일의 `<script>` 블록이 웹팩의 /\.js\$/ 의 바벨 로더에도 적용될 수 있다.

webpack.config.js에 아래를 추가해주자.

```js
const VueLoaderPlugin = require("vue-loader/lib/plugin");

module.exports = {
  mode: "development",
  plugins: [new VueLoaderPlugin()],
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "babel-loader"
      },
      {
        test: /\.vue$/,
        loader: "vue-loader"
      }
    ]
  }
};
```

.vue 파일의 `<style>` 블록과 .css 파일을 처리하기 위해 vue-style-loader와 css-loader 를 설치하자

    npm install --save-dev vue-style-loader css-loader

webpack.config.js 파일에도 추가하자.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["vue-style-loader", "css-loader"]
      }
    ]
  }
};
```

Vue 루트 인스턴스를 `<div id="app"></div>`에 마운트 시키려면 dist/index.html 에 위 DOM이 있어야 한다. 이 작업을 위해 public/index.html 파일을 만들고 아래와 같이 작성한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Webpack App</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

webpack.config.js의 HtmlWebpackPlugin에 template 옵션을 추가하여 위 html을 기반으로 index.html이 만들어지게 설정한다. npm run build를 해보면 dist/index.html 파일에 `<div id="app"></div>`이 보일 것이다.

```js
module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      template: "public/index.html"
    })
  ]
};
```

테스트를 위해 간단히 vue-router가 적용된 vue앱을 만들어보자. 우선 vue-router을 설치한다.

    npm install --save-dev vue-router

src에 router/index.js 파일을 만들고 아래와 같이 입력한다.

```js
    // router/index.js
    import Vue from "vue"
    import VueRouter from "vue-router"
    import Hello from "../components/Hello.vue"

    Vue.use(VueRouter)

    export default new VueRouter({
      mode: "history",
      base: process.env.BASE_URL,
      routes: [
        {
          path: "/",
          name: "Hello",
          component: Hello
        }
      ]
    })

    // index.js
    import Vue from "vue";
    import App from "./App.vue";
    import router from "./router";

    new Vue({
      render: h => h(App),
      router
    }).$mount("#app");

    // App.vue
    <template>
      <div id="app">
        <router-view></router-view>
      </div>
    </template>

    // Hello.vue
    <template>
      <p>{{ greeting }} world!</p>
    </template>
    <script>
    module.exports = {
      data: function () {
        return {
          greeting: 'Hello'
        }
      }
    }
    </script>
    <style scoped>
    p {
      font-size: 2em;
      text-align: center;
    }
    </style>
```

npm run start:dev 를 했을 때 아래 화면이 보이면 제대로 설정된 것이다.

![Vue%20CLI%20Vue%20Typescript%20Babel%20Webpack%202/Untitled.png](https://raw.githubusercontent.com/devgaram/TIL/master/Vue/images/2020-03-03-img/Untitled.png)

**추가 설정**

router/index.js 에서 import Hello from "../components/Hello.vue" 를 매번 ../ 식으로 쓰는 게 귀찮을 때! webpack resolve 설정을 통해 src를 기준으로 모듈을 찾을 수 있다. webpack.config.js 에 아래와 같이 추가하면 **import Hello from "@/components/Hello.vue"** 를 사용할 수 있다. 또 .vue 확장자를 빼고 입력하고 싶다면 extensions를 추가하면 된다. 웹팩이 알아서 파일 확장자를 처리해준다.

```js
module.exports = {
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src")
    },
    extensions: [".js", ".vue"]
  }
};
```

- **resolve** : 모듈을 어떻게 처리할지 정의 만약 ES2015에서 import 'lodash' 를 사용한다면 웹팩에서 lodash를 어떻게 볼 것인지 lodash.js로 볼지 lodash.ts 로 볼 지 등을 정의한다.

> 물론 이 외에도 설정할 게 많겠지만...... 일단은 여기까지 하고 Vue에서 Typescript 사용하는 방법을 이어가려고 한다. 나중에 이 설정을 기반으로 실제로 프로젝트를 진행하게 된다면 문제가 쏟아질 듯 싶다....

## 6. Vue에서 Typescript 설치 및 설정

typescript와 ts-loader 패키지를 설치한다.

    npm install --save-dev typescript ts-loader

- typescript : 타입스크립트
- ts-loader : 웹팩 로더 패키지

webpack.config.js 에 아래 내용을 추가한다. 이제 .vue 파일 안의 ts도 .ts 파일도 ts-loader에 의해 처리된다.

```js
    module.exports = {
    	entry: './src/index.ts',
      module: {
          {
            test: /\.ts$/,
    				exclude: /node_modules/,
            loader: 'ts-loader',
            options: { appendTsSuffixTo: [/\.vue$/] }
          }
        ]
      },
      resolve: {
        extensions: ['.ts', 'tsx', '.js', '.vue']
      }
    };
```

typescript 설정을 위해 프로젝트 루트 폴더에 tsconfig.json 파일을 생성하고 아래 내용을 입력한다.

    {
      "compilerOptions": {
        "outDir": "./dist/",
        "strict": true,
        "module": "es2015",
        "target": "es5",
    		"moduleResolution": "node",
        "sourceMap": true,
        "allowSyntheticDefaultImports": true
      },
      "include": [
        "src/**/*"
      ],
      "exclude": [
        "node_modules",
        "**/*.spec.ts"
      ]
    }

- allowSyntheticDefaultImports : export default 를 export 한 값들을 가지는 객체로 설정

테스트를 위해 프로젝트 코드 수정하자. 우선 src/index.js, src/router/index.js → src/index.ts, src/router/index.ts 로 바꾼다. Hello.vue의 내용을 아래처럼 바꾼다. Vue 컴포넌트 안에서 Typescript를 사용하려면 Vue.component 또는 Vue.extend로 컴포넌트를 정의해야한다. 타입스크립트가 Vue 컴포넌트 타입 유추를 할 수 있게 하기 위해서다.

```js
    <template>
      <p>{{ greeting }} world!</p>
    </template>
    <script lang="ts">
    import Vue from 'vue'
    export default Vue.extend({
      data: function () {
        return {
          greeting: 'Hello'
        }
      }
    })
    </script>
    <style scoped>
    p {
      font-size: 2em;
      text-align: center;
    }
    </style>
```

실행하려고 하면 .vue 파일이 import 되지 않는 오류를 발견할 것이다. ts 파일에 .vue 파일을 임포트하려면 추가적인 설정이 필요하다. src/shims-vue.d.ts 파일을 생성하여 이렇게 적자.

```js
    declare module "*.vue" {
      import Vue from "vue";
      export default Vue;
    }
```

저장 후 실행해보면 잘된다.

다음 TIL에서는 vue-class-component 데코레이터를 사용하여 컴포넌트를 선언할 때 클래스 기반 API를 이용하는 방식을 적용해봐야지!
