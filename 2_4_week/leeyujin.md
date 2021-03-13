# React에서 URL 다루기
## SPA에서 Routing

- 전통적인 웹페이지의 경우 각 URL로 HTML을 요청하면 각 URL에 맞춰서 새로운 HTML을 내려주고 브라우저가 렌더링합니다.
- SPA(Single Page Application)의 경우, 최초 페이지로 접근할 때 모든 HTML,CSS,Javascript를 받아오고 SPA 프레임워크가 각 URL에 따라 필요한 부분만 업데이트하는 구조입니다.
- 정해진 url에 접근하면 맞는 컴포넌트로 갈아끼워서 웹페이지를 재구성합니다. 이러한 기능을 라우팅이라 합니다.
- React에서는 이러한 기능을 따로 제공하지 않습니다. 

## Router 컴포넌트

- `Router` 컴포넌트는 `Link`,`Route`컴포넌트를 동작하기 위해 반드시 필요한 컴포넌트입니다.
- `Router`를 상위 컴포넌트로 가지고 있어야 `Route` 컴포넌트가 동작합니다.

```jsx

import { BrowserRouter as Router, Route, Link } from "react-router-dom";

// Router 컴포넌트 사용 예시
<Router>
    <Link to="/">Home</Link>
    <Link to="mypage">Mypage</Link>
</Router>

// Router 컴포넌트 사용 예시
<Router>
    <Route path="/" component={Home} />
    <Route path="mypage" component={MyPage} />
</Router>

```
## Route 컴포넌트

- Route 컴포넌트는 정해진 URL로 고객이 접근했을 때 약속된 컴포넌트를 렌더링하는 기능을 제공합니다.
- `path`에는 컴포넌트를 렌더링할 url을 `component`에는 `path`에 약속된 url로 접근했을 때 렌더링할 컴포넌트를 입력합니다.

```jsx

import React from "react";
import { BrowserRouter as Router, Route } from "react-router-dom";

// Home, MyPage, About 컴포넌트 생성
const Home = () => <div>Home</div>;
const MyPage = () => <div>MyPage</div>;
const About = () => <div>About</div>;

// 각 URL로 보여줄 컴포넌트 매칭
const App = () => {
    return (
        <Router>
            <Route path="/mypage" component={MyPage} />
            <Route path="/about" component={About} />
            <Route path="/" exact component={Home} />
        </Router>
    );
};

export default App;

```
- `Route`에는 props로 `exact`를 넣어주었는데 이유는 `mypage`,`about`에도 `/`가 포함이 되어있습니다.
- `Home` 컴포넌트가 렌더링 됩니다. 이 문제를 해결하기 위해서 path가 `/`일 때만 `Home`컴포넌트를 렌더링하도록 옵션을 넣어주었습니다.
- `localhost:3000/mypage, localhost:3000/about, localhost:3000/`으로 접속하면 확인 가능

## Link 컴포넌트

- Link 컴포넌트는 HTML에서 사용하는 a태그와 기능이 비슷합니다.
- 해당 태그를 클릭하면 정해진 URL로 페이지를 이동시켜 줍니다.
- a 태그는 기본적으로 페이지를 이동시키면서 페이지 전부를 새로 불러옵니다.
- Link태그는 브라우저는 URL만 바꾸고 필요한 컴포넌트만 업데이트 하기 때문에 a태그와 같은 문제가 발생하지 않습니다.

## 브라우저의 히스토리

- 브라우저를 키고 이곳 저곳 웹페이지를 돌아다닌 기록을 말한다.
- 브라우저에서 뒤로가기를 눌렀을때 브라우저는 이전 웹 페이지를 기억하고 있다가 보여줍니다.
- 우리는 이전보다 훨씬 빠른 속도로 페이지가 로드되는 것을 확인할 수 있습니다.
- DOM의 window객체는 history객체를 통해서 브라우저의 히스토리에 접근할 수 있습니다.

## window.history

- `HTML`의 `window`객체에는 `history`객체가 있습니다. 여기에는 브라우저에서 유저가 이곳 저곳을 다녔던 기록이 남아있는 객체입니다.

```jsx

// 뒤로 가기
window.histroy.back();

// 앞으로 가기
window.histroy.forward();

// 특정 위치로 가기
window.history.go(-1);
// window.history.back()과 동일하게 한 페이지 뒤로 이동합니다.
window.history.go(1);
// window.history.forward()와 동일하게 한 페이지 앞으로 이동합니다.

window.history.go(-2);
// 두 페이지 이전으로 이동합니다.

```

- 브라우저의 히스토리 기록을 이동할 수 있으며 length 속성을 이용해서 히스토리가 몇 페이지로 되어있는지 확인할 수 있습니다.

```jsx

console.log(window.history.length);
// 현재 페이지를 기준으로 앞,뒤 페이지가 없다면 1이 출력됩니다.

```

## 히스토리 추가 및 변경

- HTML5는 히스토리 엔트리를 추가하거나 변경하기 위해 pushState()와 replaceState()라는 메서드를 제공합니다.
- window.onpopstate 이벤트를 연동하여 동작합니다.
- pushState는 히스토리 엔트리에 새로운 엔트리를 추가하는 메서드입니다.
- replaceState는 현재 히스토리 엔트리를 변경합니다.
- history.pushState(),history.replaceState()두 개의 메서드는 모두 3개의 파라미터(매개변수)를 가지고 호출할 수 있습니다.

```jsx

history.pushState({data: 'pushState1'}, '', '/pushState1');
history.replaceState({data: 'replaceState'}, '', 'replace-state');

```

- 여기에서 첫번째 파라미터의 경우 history.state에 기록될 데이터입니다.
- 두번째 파라미터의 경우 타이틀 값인데 아직 사용이 정확하게 정의되거나 구현되어있지 않은 경우입니다.
- 세번째 인자는 새로운 히스토리 엔트리의 URL값입니다.
- 파라미터로 절대 경로는 '/abc'로 지정했는데 상대경로로도 설정할 수 있습니다.
- 첫번째 파라미터로 입력된 데이터 값은 history.state에 객체 사본으로 사용됩니다.

```jsx
<!doctype html>
<html lang="ko">
  <head>
    <title>History API Sample</title>
    <meta charset="utf-8">
    <style>
      section {margin: 20px 0;}
    </style>
  </head>
  <body>
    <section>
      <h2>push state</h2>
      <button id="push-state1">pushState1</button>
      <button id="push-state2">pushState2</button>
      <button id="push-state3">pushState3</button>
    </section>
    <section>
      <h2>replace state</h2>
      <button id="replace-state">replaceState</button>
    </section>
    <section>
      <h2>history state</h2>
      <span id="state"></span>
    </section>
  </body>
</html>
```

```jsx
// 현재의 history state 값을 출력합니다.
const currentHistoryState = () => {
  document.getElementById('state').innerText = JSON.stringify(history.state);
};
 
currentHistoryState();
 
// pushState()
document.getElementById('push-state1').addEventListener('click', () => {
  history.pushState({data: 'pushState1'}, '', '/push-state1');
  currentHistoryState();
});
document.getElementById('push-state2').addEventListener('click', () => {
  history.pushState({data: 'pushState2'}, '', '/push-state2');
  currentHistoryState();
});
document.getElementById('push-state3').addEventListener('click', () => {
  history.pushState({data: 'pushState3'}, '', '/push-state3');
  currentHistoryState();
});
 
// replaceState()
document.getElementById('replace-state').addEventListener('click', () => {
  history.replaceState({data: 'replaceState'}, '', '/replace-state');
  currentHistoryState();
});
 
// 브라우저의 뒤로가기 / 앞으로가기를 누르면 history state 값을 확인하여 출력합니다.
window.addEventListener('popstate', () => {
  currentHistoryState();
});
```
- push state버튼을 누르면 화면을 새로 그리지 않고 URL이 파라미터로 보낸 URL값으로 변경되고 히스토리 엔트리에 추가됩니다.
- 뒤로가기나 앞으로가기를 누르면 이전의 URL로 다시 화면을 새로 그리지 않고 이동할 수 있습니다.
- replace state버튼은 새로운 앤트리를 추가하지 않고 현재의 엔트리 값을 변경하기 때문에 뒤로가기로 이전 URL로 이동할 수 없습니다.

1. pushState()
- push-state1 클릭합니다.
- URL이 /push-state1로 변경됩니다.
- pushState2 클릭
- URL이 /push-state2로 변경됩니다.
- 브라우저에서 뒤로가기를 누릅니다.
- URL이 /push-state1로 변경됩니다.

2. replaceState()
- pushState1 클릭합니다.
- URL이 /push-state1로 변경됩니다.
- pushState2 클릭
- URL이 /push-state2로 변경됩니다.
- replace-state 클릭
- URL이 /replace-state로 변경됩니다.
- 브라우저에서 뒤로가기를 누릅니다.
- URL이 /push-state1로 변경됩니다.

