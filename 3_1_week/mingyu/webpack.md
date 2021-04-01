---
title: Webpack
author: mingyu.gu
date: 2021.03.21
---

# Webpack
 
## 웹팩의 등장 배경
웹팩이 등장한 이유는 크게 3가지 입니다.
1. 파일 단위의 자바스크립트 모듈 관리의 필요성
2. 웹 개발 작업 자동화 도구 (Web Task Manager)
3. 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

### 파일 단위의 자바스크립트 모듈 관리
자바스크립트의 변수 유효 범위는 기본적으로 전역 범위를 갖습니다. 최대한 넓은 변수 범위를 갖기 때문에 어디에서도 접근하기 편리합니다.

이러한 부분이 실제 웹 애플리케이션을 개발할 때는 아래와 같은 문제로 변합니다.
```html
<!-- index.html -->
<html>
  <head>
    <!-- ... -->
  </head>
  <body>
    <!-- ... -->
    <script src="./app.js"></script>
    <script src="./main.js"></script>
  </body>
</html>
```
```js
// app.js
var num = 10;
function getNum() {
  console.log(num);
}
```
```js
// main.js
var num = 20;
function getNum() {
  console.log(num);
}
```
위와 같이 `index.html` 에서 두 자바스크립트 파일을 로딩하여 사용한다고 할 때, 스크립트에서 아래와 같이 코드를 실행하면 어떤 결과가 나올까요?
```html
<!-- index.html -->
<html>
  <head>
    <!-- ... -->
  </head>
  <body>
    <!-- ... -->
    <script src="./app.js"></script>
    <script src="./main.js"></script>
    <script>
      getNum(); // 20
    </script>
  </body>
</html>
```
결과는 20입니다. `app.js`에서 선언한 `num` 변수는 `main.js`에서 다시 선언하고 20을 할당했기 때문입니다.

이러한 문제점은 실제로 복잡한 애플리케이션을 개발할 때 발생합니다. 변수의 이름을 모두 기억하지 않은 이상 변수를 중복 선언하거나 의도치 않은 값을 할당할 수 있습니다.

함수 스코프를 사용하는 자바스크립트는 이러한 문제점을 해결하기 위해 즉시호출함수(IIFE)를 사용해 모듈을 만들 거나 CommonJS나 AMD 스타일의 모듈 시스템을 사용하여 파일별로 모듈을 관리하였습니다.

### 웹 개발 작업 자동화 도구
이전부터 프런트엔드 개발 업무를 할 때 가장 많이 반복하는 작업은 텍스트 편집기에서 코드를 수정하고 저장한 뒤 브라우저에서 새로 고침을 누르는 것이었습니다. 그래야 화면에 변경된 내용을 볼 수 있었습니다.

이외에도 웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다.
- HTML, CSS, JS 압축
- 이미지 압축
- CSS 전처리기 변환
이러한 일들을 자동화 해주는 도구들이 필요했습니다. 그래서 Grunt와 Gulp 같은 도구들이 등장했습니다.

### 웹 애플리케이션의 빠른 로딩 속도와 높은 성능
예전부터 웹 사이트의 로딩 속도를 높이기 위해 많은 노력들이 있었고, 그 중 대표적인 노력이 브라우저에서 서버로 요청하는 파일 숫자를 줄이는 것입니다. 이를 위해 앞에서 살펴본 웹 태스크 매니저를 이용해 파일들을 압축하고 병합하는 작업들을 진행했습니다.

뿐만 아니라 초기 페이지 로딩 속도를 높이기 위해 나중에 필요한 자원들은 나중에 요청하는 레이지 로딩(Lazy Loading)이 등장했죠.

웹팩은 기본적으로 필요한 자원은 미리 로딩하는게 아니라 그 때 그 때 요청하자는 철학을 갖고 있습니다.

## 웹팩으로 해결하려는 문제?
웹팩에서 해결하고자 하는 기존의 문제점은 다음 4가지 입니다.
- 자바스크립트 변수 유효 범위
- 브라우저별 HTTP 요청 숫자의 제약
- 사용하지 않는 코드의 관리
- Dynamic Loading & Lazy Loading 미지원

### 자바스크립트 변수 유효 범위 문제
웹팩은 변수 유효 범위의 문제점을 ES6의 Modules 문법과 웹팩의 모듈 번들링으로 해결합니다.

### 브라우저별 HTTP 요청 숫자의 제약
TCP 스펙에 따라 브라우저에서 한 번에 서버로 보낼 수 있는 HTTP 요청 숫자는 제약되어 있습니다. 아래의 표는 최신 브라우저 별 최대 HTTP 요청 횟수입니다.
| 브라우저 | 최대 연결 횟수 |
|:---:|:---:|
|익스플로어 7| 2 |
|익스플로러 8 ~ 9|	6 |
|익스플로러 10, 11|	8, 13 |
|크롬|	6 |
|사파리|	6 |
|파이어폭스|	6 |
|오페라|	6 |
|안드로이드, iOS|	6 |
따라서, HTTP 요청 숫자를 줄이는 것이 웹 애플리케이션의 성능을 높여줄 뿐만 아니라 사용자가 사이트를 조작하는 시간을 앞당겨 줄 수 있습니다.

## 성능 올리기
> 모든 웹 페이지의 속도를 결정 짓는 첫번째 요소는 첫 페이지를 그릴 때 필요한 자원양이다.

Webpack 같은 bundler들은 모든 JS코드를 하나의 거대한 파일로 만들어내었고, 이는 웹 페이지에서 페이지를 그릴때 필요한 자원의 양 또한 크게 증가하게 되었습니다.

웹 페이지에서는 큰 JS파일을 모두 불러올 때까지 Rendering을 멈추어, 첫 페이지를 그리기까지 오래 걸리기 때문에 웹페이지 경험에 좋지않은 영향을 끼치게 됩니다.

때문에 웹사이트의 속도를 올리기 위해서는 큰 JS파일의 용량을 줄여야하는데, 크게 코드의 크기를 줄이는 방법과 코드를 분할하는 방법이 있습니다.

## 코드 줄이기
코드를 줄이기 위해서는 렌더링에 필요없는 코드를 줄일 필요가 있습니다.

불필요한 공백이나, 줄바꿈 혹은 주석과 같은 렌더링 과정에 필요없는 코드를 제거할 수 있는데 이를 Minify(압축)라고 부릅니다.

`webpack.optimization` 중 `minimizer` 옵션을 이용해 압축하거나 `uglify-js`를 이용할 수 있습니다.

압축과정은 일반적인 코드들을 제거할 수 있지만, 중복되거나, 사용되지 않는 코드까지 제거하지 못합니다.

이 경우 Tree Shaking으로 사용되지 않는 코드를 제거할 수 있습니다.

`Webpack`은 Code를 Build하는 시점에 import된 module중 사용하지 않는 코드를 제거할 수 있습니다.

## 코드 분할하기 Code Splitting
번들된 파일을 여러 페이지에서 필요하나 파일로 나누고, 작은 화면에서 필요한 시점마다 불로올 수 있도록 나눈다면, 첫 렌더링에 필요한 파일의 크기를 줄여 더 빠르게 렌더링 할 수 있습니다.

### Webpack의 Entry와 SplitChunksPlugin
Webpack에서는 코드를 분할 하기위해, 목적 별로 여러 `Entry`로 분할 할 수 있습니다.

페이지 별로 분할 할 수 있고, 특정 렌더링을 위한 코드로 분할하여, 각 페이지에서 사용할 수도 있스빈다.
```js
// webpack.config.js
module.exports = {
  entry: {
    home: 'home.js',
    about: 'about.js'
  }
}
```
분할된 `Entry`파일에는 각 `Entry`들이 필요한 여러 모듈들을 모두 포함하고 있습니다.

분할에는 성공했지만, 전체 크기로 봤을 때는 배로 커지게 됩니다.

때문에, 중복된 코드도 분할하여, 관리할 필요가 있는데, `webpack`의 `SplitCunksPlugin`를 이용하면 쉽게 분할 할 수 있습니다.

```js
// webpack.config.js
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        commons: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
};
```

`commons`에 설정한 값은 각각의 `entry`가 공통적으로 사용하여, 중복되는 `module`들을 `chunk` 파일로 추출합니다.

이렇게 열심히 분할한 `entry`와 `chunk`파일도 한 화면에서 필요한 요소보다 더 많은 화면들을 그리게 됩니다.

### 동적 코드 분할 Dynamic Imports
대부분의 코드들은 사용자가 보는 첫 페이지에는 필요하지 않습니다.
첫 페이지 진입시에 필요한 최소한의 코드만 다운 받고, 사용자가 특정 페이지나 위치에 도달할 때마다 코드를 로드한다면, 첫 페이지의 초기 성능을 올릴 수 있습니다.

이런 방식을 lazy-loading이라고 합니다.

`Dynamic Import`를 이용하면 런타임시에 필요한 `module`을 `import`할 수 있습니다.
```js
import('module.js')
  .then((module) => {
    const { default: Component, a, b } = module;
  });

(async () => {
  await import('module.js');
  const { default: Component, a, b } = await import('module.js');
})();
```
이를 이용하면, 거대한 하나의 JS를 여러 JS로 쪼갤 수 있게되고, 화면이 위치할 때마다 사용자가 클릭할 때마다 Import함으로써 분할하여 관리할 수 있습니다.

Dynamic Import는 아직 모든 브라우저에서 지원하지 않기에 Webpack 같은 Bundler를 이용하면 매우 쉽게 구현할 수 있습니다.

Webpack 에서 `Dynamic Import`를 사용하기 위해서는 `babel-plugin-syntax-dynamic-import` 플러그인이 필요합니다.
```js
// babel.config.json
{
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```
Build 시점에 `import()`모듈을 `chunk` 파일로 만들어 필요한 시점에 `header`에 script를 세팅하여 JS파일들을 다운로드 합니다.
```html
<script charset="utf-8" src="/static/js/2.chunk.js"></script>
```
이때 `chunk`파일 명은 `webpack`의 `output`에 세팅 한 파일명을 따라갑니다.
```js
module.exports = {
  output: {
    filename: '[name].js',
    chunkFilename: '[name].chunk.js',
  }
}
```
만약 파일명을 지정하고 싶다면, 주석을 추가하여 파일명을 명시적으로 지정할 수 있습니다.
```js
import(/* webpackChunkName: "test" */ 'module.js')
  .then((module) => {
    const { default: Component, a, b } = module;
  });
```

### Dynamic Import 시점 (prefetch / preload)
첫 페이지에는 필요하지 않지만 앞으로 필요할 만한 JS를 미리 다운로드 받아둘 수도 있습니다.
- Preload: 프리로드는 호출 시점에 브라우저가 반드시 가져오게 합니다.
- Prefetch: 프리패치는 호출시점에 가져오지 않고 브라우저가 여유가 될 때 가져오게 합니다.

```js
import(/* webpackPreload: true */ 'module.js');
import(/* webpackPrefetch: true */ 'module.js')
  .then((module) => {
    const { default: Component, a, b } = module;
  })
```
이때 webpackPrefetch가 먼저 호출되었더라도 webpackPreload가 먼저 처리됩니다.
```html
<link rel="preload" as="script" href="module.js">
<link rel="prefetch" as="script" href="module.js">
```

### Reference
- [웹팩이 필요한 이유::웹팩 핸드북](https://joshua1988.github.io/webpack-guide/motivation/problem-to-solve.html#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EB%B3%84-http-%EC%9A%94%EC%B2%AD-%EC%88%AB%EC%9E%90%EC%9D%98-%EC%A0%9C%EC%95%BD)
- [웹팩의 기본 개념::김정환블로그](https://jeonghwan-kim.github.io/js/2017/05/15/webpack.html)
- [Dynamic Import로 웹페이지 성능올리기::박성룡(Andrew park)](https://pks2974.medium.com/dynamic-import-%EB%A1%9C%EC%9B%B9%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%84%B1%EB%8A%A5-%EC%98%AC%EB%A6%AC%EA%B8%B0-caf62cc8c375)
