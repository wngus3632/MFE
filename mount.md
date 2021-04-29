# mount,unmount,bootstrap

남기훈
<br>
single-spa 등록 응용 프로그램은 HTML 페이지가 없다는 점을 제외하고는 일반 SPA와 같은 모든 것입니다. single-spa 세계에서 SPA에는 각각 고유 한 프레임 워크가있는 등록 된 여러 응용 프로그램이 포함됩니다. 등록 된 응용 프로그램에는 자체 클라이언트 측 라우팅과 자체 프레임 워크 / 라이브러리가 있습니다. 그들은 자신의 HTML을 렌더링하고 마운트 될 때마다 원하는 것을 할 수있는 완전한 자유를가집니다. 마운트되는 개념은 등록 된 애플리케이션이 DOM에 컨텐츠를 넣는 지 여부를 나타냅니다. 등록 된 애플리케이션이 마운트되었는지 결정하는 것은 활동 기능입니다. 등록 된 애플리케이션이 마운트되지 않을 때마다 마운트 될 때까지 완전히 휴면 상태를 유지해야합니다.

### 등록 된 애플리케이션 생성

등록 된 애플리케이션을 생성하려면 먼저 single spa에 애플리케이션을 등록하십시오. 등록 된 애플리케이션은 기본 진입 점 내에서 다음의 모든 lifecycle 기능을 올바르게 구현해야합니다.

### 등록 된 애플리케이션 lifecycle

single spa 페이지 과정에서 등록 된 애플리케이션이로드, 부트 스트랩 (초기화), 마운트, 마운트 해제 및 언로드됩니다. single spa는 lifecycle을 통해 각 단계에 대한 후크를 제공합니다.
lifecycle함수는 single spa가 등록 된 애플리케이션에서 호출하는 함수 또는 함수 배열입니다. single spa는 등록 된 애플리케이션의 기본 파일에서 특정 이름이 지정된 내보내기를 찾아 이를 호출합니다.

-note 1)부트 스트랩, 마운트 및 마운트 해제를 구현해야합니다. 그러나 언로드 구현은 선택 사항입니다. 2)각 lifecycle 함수는 Promise를 반환하거나 비동기 함수여야 합니다. 3)함수 배열을 내 보내면 (단지 하나의 함수 대신) 함수가 하나씩 호출되어 다음 함수를 호출하기 전에 한 함수의 promise가 해결 될 때까지 기다립니다.
4)single spa가 시작되지 않으면 애플리케이션이 로드되지만 부트 스트랩, 마운트 또는 마운트 해제되지 않습니다.

### Lifecyle props

lifecycle 함수는 몇 가지 보장된 정보와 추가 사용자 지정 정보가 포함된 객체인 props 인수로 호출됩니다.

```
function bootstrap(props) {
  const {
    name, // The name of the application
    singleSpa, // The singleSpa instance
    mountParcel, // Function for manually mounting
    customProps, // Additional custom information
  } = props; // Props are given to every lifecycle
  return Promise.resolve();
}
```

name : single spa에 등록 된 문자열 이름입니다.
singleSpa : singleSpa 인스턴스 자체에 대한 참조입니다. 이는 애플리케이션 및 도우미 라이브러리가 이를 가져오지 않고도 singleSpa API를 호출 할 수 있도록하기위한 것입니다. 이는 singleSpa의 한 인스턴스만 로드되도록 설정되지 않은 여러 웹팩 구성이있는 상황에서 유용합니다.
mountParcel : mountParcel 함수.

single spa에서 제공하는 내장 된 소품 외에도 선택적으로 애플리케이션에 전달할 사용자 지정 소품을 지정할 수 있습니다. 이러한 customProps는 각 lifecycle 메소드로 전달됩니다. 사용자 지정 소품은 개체이며 개체 또는 개체를 반환하는 함수를 제공 할 수 있습니다. 사용자 정의 prop 함수는 애플리케이션 이름과 현재 window.location을 인수로 사용하여 호출됩니다.

```javascript
singleSpa.registerApplication({
  name: "app1",
  activeWhen,
  app,
  customProps: { authToken: "d83jD63UdZ6RS6f70D0" },
});

singleSpa.registerApplication({
  name: "app1",
  activeWhen,
  app,
  customProps: (name, location) => {
    return { authToken: "d83jD63UdZ6RS6f70D0" };
  },
});
```

```
export function mount(props) {
  // do something with the common authToken in app1
  console.log(props.authToken);
  return reactLifecycles.mount(props);
}
```

모든 하위 앱과 공통 액세스 토큰 공유
렌더링 대상과 같은 일부 초기화 정보를 전달합니다.
공통 이벤트 버스에 대한 참조를 전달하여 각 앱이 서로 통신 할 수 있도록합니다.

### load

등록된 애플리케이션이 지연로드되는 경우, 등록 된 애플리케이션의 코드를 서버에서 가져 와서 실행하는 경우를 의미합니다. 이것은 등록 된 애플리케이션의 활동 함수가 처음으로 진실 값을 반환하면 발생합니다. 로드 중에는 가능한 한 적게 / 아무것도하지 않고 부트 스트랩 lifecycle 기능이 작업을 수행 할 때까지 기다리는 것이 가장 좋습니다. 로드하는 동안 작업을 수행해야하는 경우 코드를 등록 된 애플리케이션의 기본 진입점에 넣지만 내보낸 함수 내부에는 넣지 마십시오.

```
console.log("The registered application has been loaded!");

export async function bootstrap(props) {...}
export async function mount(props) {...}
export async function unmount(props) {...}
```

### 부트스트랩

이 lifecycle 함수는 등록 된 애플리케이션이 처음 마운트되기 직전에 한 번 호출됩니다.

```
export function bootstrap(props) {
  return Promise.resolve().then(() => {
    // One-time initialization code goes here
    console.log('bootstrapped!');
  });
}
```

### 마운트

이 lifecycle 함수는 등록된 애플리케이션이 마운트되지 않을 때마다 호출되지만 해당 활동 함수는 진실 값을 반환합니다. 이 함수가 호출되면 URL을보고 활성 경로를 결정한 다음 DOM 요소, DOM 이벤트 리스너 등을 만들어 사용자에게 콘텐츠를 렌더링해야합니다. 모든 후속 라우팅 이벤트 (예 : hashchange 및 popstate)는 더 많은 호출을 마운트하도록 트리거하지 않지만 대신 애플리케이션 자체에서 처리해야합니다

```
export function mount(props) {
  return Promise.resolve().then(() => {
    // Do framework UI rendering here
    console.log('mounted!');
  });
}
```

### 언마운트

이 lifecycle 함수는 등록된 애플리케이션이 마운트 될 때마다 호출되지만 해당 활동 함수는 잘못된 값을 반환합니다. 이 함수가 호출되면 등록된 애플리케이션이 마운트 될 때 생성 된 모든 DOM 요소, DOM 이벤트 리스너, 누수 된 메모리, 전역, 관찰 가능한 구독 등을 정리해야합니다.

```
export function unmount(props) {
  return Promise.resolve().then(() => {
    // Do framework UI unrendering here
    console.log('unmounted!');
  });
}
```

### 언로드

1)언로드 lifecycle는 선택적으로 구현된 lifecycle 기능입니다. 응용 프로그램을 언로드해야 할 때마다 호출됩니다. 누군가가 unloadApplication API를 호출하지 않는 한 이것은 발생하지 않습니다. 등록 된 애플리케이션이 언로드 lifecycle를 구현하지 않는 경우 앱 언로드가 작동하지 않는다고 가정합니다. 2)언로드 lifecycle의 목적은 single spa 애플리케이션이 언로드되기 직전에 로직을 수행하는 것입니다. 애플리케이션이 언로드되면 애플리케이션 상태는 NOT_LOADED가되고 애플리케이션이 다시 부트스트랩 됩니다. 3)언로드의 동기는 등록 된 전체 애플리케이션의 핫 로딩을 구현하는 것이었지만 애플리케이션을 다시 부트스트랩하려는 경우에도 다른 시나리오에서도 유용하지만 애플리케이션이 다시 부트스트랩되기 전에 일부 로직을 수행합니다.

```javascript
export function unload(props) {
  return Promise.resolve().then(() => {
    // Hot-reloading implementation goes here
    console.log("unloaded!");
  });
}
```

### 타임아웃

기본적으로 등록된 애플리케이션은 글로벌 제한 시간 구성을 따르지만 특정 애플리케이션에 대해 해당 동작을 재정의할 수 있습니다. 등록 된 응용 프로그램의 기본 진입점에서 시간 초과 개체를 내보내면 됩니다

```javascript
export function bootstrap(props) {...}
export function mount(props) {...}
export function unmount(props) {...}

export const timeouts = {
  bootstrap: {
    millis: 5000,
    dieOnTimeout: true,
    warningMillis: 2500,
  },
  mount: {
    millis: 5000,
    dieOnTimeout: false,
    warningMillis: 2500,
  },
  unmount: {
    millis: 5000,
    dieOnTimeout: true,
    warningMillis: 2500,
  },
  unload: {
    millis: 5000,
    dieOnTimeout: true,
    warningMillis: 2500,
  },
};
```

millis는 최종 콘솔 경고에 대한 밀리 초 수를 나타내며 warningMillis는 경고가 콘솔에 최종 콘솔 경고까지 이어지는 간격으로 인쇄되는 밀리 초 수를 나타냅니다.

### 응용 프로그램 간 전환

1)응용 프로그램이 마운트 및 마운트 해제될 때 전환을 추가하려는 경우 부트스트랩, 마운트 및 마운트 해제 lifecycle 방법에 연결하는 것이 좋습니다. 이 single spa 전환 저장소는 앱이 마운트 및 마운트 해제될 때 전환을 추가하기 위해 이러한 lifecycle 방법에 연결하는 방법에 대한 작은 개념 증명입니다. 2)마운트 된 애플리케이션 내의 페이지 전환은 애플리케이션 자체에서 전적으로 처리할 수 ​​있습니다. 예를 들어 React 기반 프로젝트에 react-transition-group을 사용합니다.

### 예제

root-config: 마이크로 프런트 엔드용 공유 HTML 레이아웃
navbar: 탐색 책임이있는 기본 Svelte 애플리케이션
home: 로그인 한 사용자를위한 기본 React 애플리케이션
login: 로그 아웃 한 사용자를위한 기본 React 애플리케이션
auth: Rxjs 및 일반 JavaScript를 사용하는 유틸리티 모듈
1)example-home.js

```javascript
export const { bootstrap, mount, unmount } = lifecycles;
```

2)example-login.js

```javascript
export const { bootstrap } = lifecycles;

export const mount = [
  lifecycles.mount,
  () => {
    styles.use();
    return Promise.resolve();
  },
];

export const unmount = [
  lifecycles.unmount,
  () => {
    styles.unuse();
    return Promise.resolve();
  },
];
```

3)example-navbar.js

```javascript
export const { bootstrap, mount, unmount } = svelteLifecycles;
```
