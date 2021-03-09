# 3주차 - 2021.03.07 (Webpack)

WebPack을 더 공부해보았지만, 접목시키기는 힘들었다. 그래서 Nomad Coder에서 Twitter Clone을 해보았다. 

# Webpack

![3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20-%202021%2003%2007%20(Webpack)%20c0ba118e360945dd9791af481e7101db/1CNeQyaChrTh0H3ovOd9Dgg.png](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20-%202021%2003%2007%20(Webpack)%20c0ba118e360945dd9791af481e7101db/1CNeQyaChrTh0H3ovOd9Dgg.png)

- WebPack = Module Bundler라고 할 수 있음. (정확히는 Static Module Bundler)
- 웹팩에서 모든 것은 모듈이다. 자바스크립트, 스타일시트, 이미지 등 모든 것을 자바스크립트 모듈로 로딩해서 사용한다.

→ Module Bundler란 무엇 인가?

### Module

- 프로그램을 구성하는 구성 요소의 일부
- 관련된 데이터와 함수들이 묶여서 모듈을 형성하고 파일 단위로 나뉘는 것이 일반적
- 모듈화 프로그래밍은 기능별로 파일을 나눠가며 프로그래밍을 하는 것으로 유지보수가 쉽다는 장점이 있다.

### Bundler

- 번들러는 지정한 단위로 파일들을 하나로 만들어서 요청에 대한 응답으로 전달할 수 있는 환경을 만들어주는 역할을 한다.
- 번들러를 사용하면 소스 코드를 모듈별로 작성할 수 있고 모듈간 또는 외부 라이브러리의 의존성도 쉽게 관리할 수 있다.

### Webpack의 주요 4가지 개념

- Entry, Output, Loader, Plugin

1. Entry
- 의존성 그래프의 시작점을 웹팩에서는 엔트리(Entry)라고 한다.
- 웹팩은 엔트리를 통해서 필요한 모듈을 로딩하고 하나의 파일로 묶는다.
- 여러개의 엔트리가 존재할 수 있다.

2. OutPut

- 엔트리에 설정한 자바스크립트 파일을 시작으로 하나로 묶는다. 그후 번들된 결과물을 처리할 위치를 output에 기록한다.

3. Loader

- 웹팩은 오직 JavaScript와 Json만 이해할 수 있다.
- 로더는 다른 Type의 파일(img, font, stylesheet 등)을 웹팩이 이해하고 처리 가능한 모듈로 변환 시키는 작업을 한다.

4. Plugin

- 로더가 파일단위로 처리하는 반면 플러그인은 번들된 결과물을 처리한다.
- 로더가 변환하는 동안 플러그인은 bundle optimization, asset management and injection of environment과 같은 일을 진행할 수 있다.

### Browsify, Grunt, Gulp 등의 도구들은 webpack과 무슨 차이가 있을까?

- 크고 복잡하며 다양한 리소스들이 들어있는 프로젝트에는 webpack을 사용하는 것이 좋습니다.
- Grunt, Gulp는 오로지 리소스들에 대한 툴로 사용되며 dependency graph에 대한 개념이 없습니다.
- Browsify는 비슷한 도구이지만 속도면에서 webpack이 더 좋습니다.

## Twitter Clone Coding

1. Basic Setting (리액트 설치)

```jsx
npx create-react app
```

  2. Firebase SDK 설치

```jsx
npm install --save firebase
```

```jsx

import firebase from "firebase/app";

const firebaseConfig = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGEING_SENDER_ID,
    appId: process.env.REACT_APP_APP_ID,
    measurementId: process.env.REACT_APP_MEASUREMENT_ID
  };
  // Initialize Firebase
export default firebase.initializeApp(firebaseConfig);
```

![3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20-%202021%2003%2007%20(Webpack)%20c0ba118e360945dd9791af481e7101db/_2021-03-07__10.38.39.png](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20-%202021%2003%2007%20(Webpack)%20c0ba118e360945dd9791af481e7101db/_2021-03-07__10.38.39.png)

3. Firebase 설치 확인 

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import firebase from "./firebase"
console.log(firebase);

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

4. env 폴더 만들기

- public repository에 올라갈때 git hub에 API key등이 노출 되지 않기 위해, 현재 단계에서는 도메인을 켜게 되면 노출 된다.
- env파일을 만들어서 git ignore에 추가하여 노출을 방지하자.

```jsx
REACT_APP_API_KEY=AIzuEWcI__Y29lZBxA
REACT_APP_AUTH_DOMAIN=debaseapp.com
REACT_APP_PROJECT_ID=dwittfe6
REACT_APP_STORAGE_BUCKET=dwom
REACT_APP_MESSAGEING_SENDER_ID=3065615
REACT_APP_APP_ID=1:30656188455:web:79b69efb93e02
REACT_APP_MEASUREMENT_ID=G-Y6
```

5. Setup Router

```jsx
react router dom 설치

 npm i react-router-dom
```

```jsx
import React from 'react';

const Auth () => <span>Auth</span>;
export default Auth;

```

아래와 같이 바꿔주면, import할때 알아서 자동 완성 해준다.

```jsx

import React from 'react';

export default () => <span>Auth</span>;
```

[https://velog.io/@hih0327/Webpack-기초](https://velog.io/@hih0327/Webpack-%EA%B8%B0%EC%B4%88)

[https://velog.io/@jeff0720/React-개발-환경을-구축하면서-배우는-Webpack-기초](https://velog.io/@jeff0720/React-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD%EC%9D%84-%EA%B5%AC%EC%B6%95%ED%95%98%EB%A9%B4%EC%84%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-Webpack-%EA%B8%B0%EC%B4%88)

[https://nesoy.github.io/articles/2019-02/Webpack](https://nesoy.github.io/articles/2019-02/Webpack)

[https://nomadcoders.co/nwitter/lobby](https://nomadcoders.co/nwitter/lobby)
