# **Vue-Genesis로 Micro Frontend 구현하기**
한국외국어대학교 일본어통번역학과 정주현<br>
한국외국어대학교 컴퓨터전자시스템공학부 남기훈</br>
</br>
## **Micro Frontend란?**

<br>

기존의 웹 프론트엔드는 같은 프레임워크 안에서 모두 한 번에 협업하는 형식인 모놀리식 방식으로 개발이 이루어졌다. 이 방식의 장점은 기술을 가지고 있는 인원들만이 유지보수 면에서 유리하다는 것이지만 동일한 문법과 사용법으로 하지 않으면 실행되지 않는다는 한계가 있다. 이는 다른 기술들을 함께 조화를 이루며 사용하기 어려웠다는 뜻이다.

<br>

![image](https://user-images.githubusercontent.com/74655724/118794924-04065200-b8d5-11eb-9f8c-9cdb0491525b.png)

<br>

이와 같은 배경에서 나타난 Micro Frontend는 한 페이지를 구성하기 위한 각 구성들을 한 번에 구현해야 했던 기존과 달리, 프론트엔드의 단일 구조들을 완전히 독립적으로 개발, 테스트, 배포, 유지보수 및 관리를 할 수 있다. 그리고 최종적으로는 한 어플리케이션에 각 단일 구조들을 통합하여 응집력 있게 최종 결과물을 구현하게 된다.

<br>

![image](https://user-images.githubusercontent.com/74655724/118794991-15e7f500-b8d5-11eb-8d32-c507a9298745.png)

<br>


## **Vue-Genesis란?**

<br>
Vue-genesis는 가장 필수적인 요소만 가지고 있는 Vue SSR(Server Side Rendering) 기반 렌더링 라이브러리이다. 따라서, 자신이 부가적으로 이용하고 싶은 기능이 있다면 그 부분만 추가적으로 설치하여 이용하면 되는 것이다. 즉, 사용하지 않는 기능을 배제하여 용량을 절약할 수 있다.
<br><br>하지만 Vue-genesis의 장점이 역으로 단점이 될 수 있다. 필수적인 요소만 가지고 있다는 것은 개발에 필요한 요소들을 일일이 추가로 설치해야 한다는 뜻이 된다. 규모가 커지다 보면 필요한 것들도 늘어나, 그것들을 모두 설치해야한다.
<br><br>한마디로 간단하게 만들 경우에 강점이 극대화 되는 것이다. 이 이점을 살려 Micro Frontend를 구현하고자 한다.

<br><br>

[![Architecture diagram](https://fmfe.github.io/genesis-docs/renderer.jpg?v=1)](https://fmfe.github.io/genesis-docs/guide/renderer.html)

<br>

Vue-Genesis에서는 크게 두 종류로 `HTML`모드, `JSON`모드로 구분된다.<br>
특히 이번 예시에서는 `HTML`모드를 중점으로 알아보고자 한다.<br>
`HTML`모드는 첫 화면을 렌더링 시키는 속도를 향상시키고, SEO에 보다 친화적으로 만들어준다.

<br>

### **Vue-Genesis 핵심 설치 요소**
Vue-Genesis를 사용하기 위해서는 다음 요소들의 설치가 필수적이다.
<br>

|Library|Version|Downloads|Explain|
|:-|:-:|:-|:-|
|[genesis-core](https://fmfe.github.io/genesis-docs/core/)|[![npm](https://img.shields.io/npm/v/@fmfe/genesis-core.svg)](https://www.npmjs.com/package/@fmfe/genesis-core) |[![npm](https://img.shields.io/npm/dm/@fmfe/genesis-core.svg)](https://www.npmjs.com/package/@fmfe/genesis-core)|기본적인 플러그인 메카니즘, SSR 렌더링 로직과 프로그램 환경을 제공|
|[genesis-compiler](https://fmfe.github.io/genesis-docs/compiler/)|[![npm](https://img.shields.io/npm/v/@fmfe/genesis-compiler.svg)](https://www.npmjs.com/package/@fmfe/genesis-compiler) |[![npm](https://img.shields.io/npm/dm/@fmfe/genesis-compiler.svg)](https://www.npmjs.com/package/@fmfe/genesis-compiler)|개발환경에서만 이용됨. 프로그램을 컴파일하고 웹팩의 핵심 로직을 다룸|
|[genesis-app](https://fmfe.github.io/genesis-docs/app/)|[![npm](https://img.shields.io/npm/v/@fmfe/genesis-app.svg)](https://www.npmjs.com/package/@fmfe/genesis-app) |[![npm](https://img.shields.io/npm/dm/@fmfe/genesis-app.svg)](https://www.npmjs.com/package/@fmfe/genesis-app)| 애플리케이션을 빠르게 만들고 `vue-router`를 wrap함|
|[genesis-remote](https://fmfe.github.io/genesis-docs/remote/)|[![npm](https://img.shields.io/npm/v/@fmfe/genesis-remote.svg)](https://www.npmjs.com/package/@fmfe/genesis-remote) |[![npm](https://img.shields.io/npm/dm/@fmfe/genesis-remote.svg)](https://www.npmjs.com/package/@fmfe/genesis-remote)| 마이크로서비스의 핵심 dependency를 수행하는 컴포넌트를 연결|
|[square](https://www.npmjs.com/package/@fmfe/square)|[![npm](https://img.shields.io/npm/v/@fmfe/square.svg)](https://www.npmjs.com/package/@fmfe/square) |[![npm](https://img.shields.io/npm/dm/@fmfe/square.svg)](https://www.npmjs.com/package/@fmfe/square)|마이크로 모듈로 디자인 된 상태 관리 라이브러리|
|[genesis-lint](https://www.npmjs.com/package/@fmfe/genesis-lint)|[![npm](https://img.shields.io/npm/v/@fmfe/genesis-lint.svg)](https://www.npmjs.com/package/@fmfe/genesis-lint) |[![npm](https://img.shields.io/npm/dm/@fmfe/genesis-lint.svg)](https://www.npmjs.com/package/@fmfe/genesis-lint)|eslint과 stylelint를 포함한 코드 가이드라인| 


<br><br>

### **그 외 추가 설치 요소**

* **express(HTTP Service)**
    >Genesis는 SSR 라이브러리이기 때문에 HTTP 서비스를 만들 능력이 없기 때문에 설치한다.
    
    >npm install express
* **ts**
    >기본적으로 Vue-Genesis는 typescript를 바탕으로 이루어져있다. 따라서 그대로 사용할 시 설치가 필요하며, javascript로 이용할 경우 문법만 고치면 된다.

    >npm install ts-node typescript -g

### **디렉토리 구조**

```
    .
    ├── genesis.ts       // 핵심 작업 실행 엔트리
    ├── genesis.build.ts // 프로덕션 환경 코드를 컴파일 한다
    ├── genesis.dev.ts   // 개발 환경 스타트업 엔트리
    ├── genesis.prod.ts  // 프로덕션 환경 시작 entrance
    ├── tsconfig.json    // TS Configuration 파일
    └── package.json
```

#### **genesis.ts**
```ts
import express from 'express';
import { SSR, Renderer } from '@fmfe/genesis-core';

/**
 * 애플리케이션을 만듦
 */
export const app = express();

/**
 * SSR 인스턴스를 만듦
 */
export const ssr = new SSR();

/**
 *  렌더러를 얻은 후 애플리케이션을 실행함
 */
export const startApp = (renderer: Renderer) => {
    /**
     * 렌더링을 하기 위해 기본 렌더링 미들웨어를 사용하거나 low-level의 renderer.renderJson를 renderer.renderHtml 불러냄 
     */
    app.use(renderer.renderMiddleware);
    /**
     * 포트 연결 대기
     */
    app.listen(3000, () => console.log(`http://localhost:3000`));
};

```
>개발 환경과 프로덕션 환경을 실행하는 기본적인 로직

#### **genesis.build.ts**

```ts
import { Build } from '@fmfe/genesis-compiler';
import { ssr } from './genesis';

const start = () => {
    /**
     * 컴파일 된 인스턴스를 생성
     */
    const build = new Build(ssr);
    /**
     *  컴파일을 실행하고 프로덕션 환경 애플리케이션 패키지를 구축
     */
    return build.start();
};
start();
```
>프로덕션 환경에서 쓰여진 코드를 컴파일

#### **genesis.dev.ts**

```ts
import { Watch } from '@fmfe/genesis-compiler';
import { ssr, app, startApp } from './genesis';

const start = async () => {
    /**
     * observation 인스턴스 생성
     */
    const watch = new Watch(ssr);
    /**
     * observation 시작
     */
    await watch.start();
    /**
     * observation 인스턴스에 상응하는 렌더러를 얻음
     */
    const renderer = watch.renderer;
    /**
     * 전형적인 자원 미들웨어
     */
    app.use(watch.devMiddleware);
    /**
     * 핫 업데이트 미들웨어
     */
    app.use(watch.hotMiddleware);
    /**
     * 렌더러를 얻고 애플리케이션 실행
     */
    startApp(renderer);
};
start();

```
>개발환경, 프로그램 시작 파일

#### genesis.prod.ts
```ts
import express from 'express';
import { ssr, app, startApp } from './genesis';

/**
 * 컴파일된 애플리케이션을 가진 프로덕션 환경이라면 여기에 바로 렌더러를 만들 수 있음
 */
const renderer = ssr.createRenderer();

/*
 * 프로덕션 환경에서 정적 리소스는 콘텐트 해시로 만들어진 파일 이름에 기반한 것이기에
 * 정적 디렉토리를 여기에 둘 때 좋은 캐시를 얻을 수 있다.
 */
app.use(
    renderer.staticPublicPath,
    express.static(renderer.staticDir, {
        immutable: true,
        maxAge: '31536000000'
    })
);

/**
 * 애플리케이션 실행
 */
startApp(renderer);
```
>프로덕션 환경 시작 파일

<br>

## **예제 구현**

<br>
원본 깃허브 주소 https://github.com/fmfe/genesis
<br> 

![image](https://user-images.githubusercontent.com/74655724/118503396-8bcd4e80-b765-11eb-8104-882c8d663807.png)

<br> 

### **특징**

1. layout에 home, about를 렌더링하여 하단에 띄우는 방식이다.<br>
2.	렌더링 기능을 이용한 부분은 위의 그림과 같은 세가지 빨간 박스의 내용들이다.<br>
3.	① Router link의 각각 ‘home’, ‘About’, ‘404’를 누르면 layout의 하단인 ②에 각각의 app의 화면으로 바뀌며, 주소도 각각 ‘localhost:3000/’, ‘localhost:3000/about’, ‘localhost:3000/404’로 이동한다.
<br> (단, ‘404’의 경우는 구현되어 있지 않아, 누르면 layout앱 밑 화면에는 아무것도 뜨지 않는다)<br>
4.	②에 표시되는 각 app은 서로 이동할 수 있는 하이퍼링크를 가진다. (home은 about으로, about은 home으로)<br>
5.	③ Router Instansce Method는 이를 바탕으로 이전 페이지, 다음 페이지로 이동할 수 있다.

<br>

### **그 외 추가 설치 요소**

* **@types/jest**
    >npm i @types/jest 
* **next-redux-wrapper**
    >npm install --save @types/node
* **redux-devtools-extension**
    >npm i redux-devtools-extension



### **예제코드**

#### **ssr-layout/src/app.vue**


```js
<script>
    import { RemoteView } from "@fmfe/genesis-remote";

    export default Vue.extend({
        name: "app",
        components: {
            RemoteView
        }
    });
</script>
```
>필수 요소로 설치한 genesis-remote를 import 한다.

```js
<templete>
<header>
    <ul class="flex justify-center items-center p-2">
        <li v-for="(navItem, index) in nav" :key="index">
            <router-link :to="navItem.path" class="route-link">{{navItem.label}}</router-link>
        </li>
    </ul>
</header>
</templete>
```

```js
<script>
data() {
    return {
        count: 0,
        nav: [
            {
                path: "/",
                label: "Home"
            },
            {
                path: "/about",
                label: "About"
            },
            {
                path: "/404",
                label: "404"
            }
        ]
    };
}
</script>
```
>①에 해당하는 코드로, ‘localhost:3000/’, ‘localhost:3000/about’, ‘localhost:3000/404’로 연결해준다.

```html
<main>
    <remote-view :key="$route.meta.remoteUrl" :fetch="fetch" />
</main>
```

```js
<script>
methods: {
    async fetch() {
        const remoteUrl: string = this.$route.meta.remoteUrl;
        const url = decodeURIComponent(this.$route.fullPath);
        const res = await axios.get(
            `http://localhost:3000${remoteUrl}?renderUrl=${url}`
        );
        if (res.status === 200) {
            return res.data;
        }
        return null;
    }
}
</script>
```
>② 자리를 차지하는 코드이다. axios로 얻은 res.data로 ②에 연결된 애플리케이션 주소를 띄운다. <br>fetch를 실행하면 server와 client가 모두 실행된다.
#### **ssr-layout/src/routes.ts**
```js
routes: [
    {
        path: '/',
        meta: {
            remoteUrl: '/api/home'
        },
        component: () => import('./app.vue')
    },
    {
        path: '/about',
        meta: {
        remoteUrl: '/api/about'
        },
        component: () => import('./app.vue')
    }
]	 
```
#### **Node_modules/@fmfe/genesis-remote/src/format.ts**
```ts
export const RemoteView = {
	    name: 'remote-view',
	    props: {
	        fetch: {
	            type: Function
	        },
	        clientFetch: {
	            type: Function
	        },
	        serverFetch: {
	            type: Function
	        }
	    }
}
```
>위의 세 코드들을 바탕으로 ssr-layout/src/app.vue에 인스턴스를 생성하고, 해당 인스턴스는 ssr-layout/src/routes.ts를 바탕으로 ②에 띄운다.

<br>



### **예제구현에서의 이슈**
1) 구현된 화면에서 상, 하단 앱을 통튼 앱을 찾고자 하였으나 찾지 못함
    > → layout의 app 하단에 home, about의 app을 띄우는 형태였음
2) 아래 코드가 렌더링에 크게 관여되었다고 파악 중이나, 어떻게 연결된 것인지 아직 알아내지 못함.

    #### **__tests__/genesis-core/renderer.ts**

    ```typescript
    class Home extends SSR {
        public constructor() {
            super({
                name: 'ssr-home',
                build: {
                        baseDir: path.resolve(__dirname, '../../examples/ssr-home')
                    }
            });
        }
    }
    class About extends SSR {
        public constructor() {
            super({
                name: 'ssr-about',
                    build: {
                    baseDir: path.resolve(__dirname, '../../examples/ssr-about')
                }
            });
        }
    }	 
    const ssr = {
        home: new Home(),
        about: new About()
    };
    ```

## 프로젝트에 Vue-Genesis를 채택하지 않은 이유
 * 개발된지 얼마 되지 않아 사용 사례가 적음
 * javascript가 아닌 typescript 기반이기 때문에 문법이 익숙치 않을 경우, 적용하기 어려울 수 있음

## 참고
> Micro Frontend 개념 이해 https://micro-frontends.org/

> Vue-Genesis 예시 https://github.com/fmfe/genesis

> Genesis-remote 사용법 참고 https://anish2690.github.io/genesis-docs-en/remote/#event-communication


