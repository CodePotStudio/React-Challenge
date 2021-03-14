## React란 무엇인가?

 - 페이스북에서 만든 편리하게 웹페이지를 만들어 주는 **Javascript** 라이브러리입니다.
 - 리액트는 UI 기능만 제공하고 있으며 전역 상태 관리, 라우팅, 빌드 시스템을 각 개발자가 직접 구축해야 한다.
 - 이러한 리액트의 진입 장벽을 낮추기 위해서 **create-react-app**을 만들었습니다.
 - API 통신이나 사용자 이벤트를 통해서 프로그램의 상탯값을 변경합니다.
 - 만일 리액트와 같은 도구를 사용하지 않는다면 브라우저의 돔을 직접 **업데이트** 해야 합니다.

```
 <라이브러리란?>

 - 다른 개발자들이 여러 사람들을 위해서 만들어 놓은 코드를 의미한다.
 - 이는 대부분의 개발자들이 모든것들을 직접 만들지 않고 만든 코드를 조합해서 사용함을 의미한다.
```

## React를 사용하면 좋은 이유?
# Single Page Application

- 단일 페이지로 구성된 웹 어플리케이션을 말한다.
- SPA는 화면 이동시에 필요한 데이터들을 서버사이드에서 HTML로 전달받지 않고 필요한 데이터들만 서버로부터 JSON 으로 전달받는다.
- 그리고 동적으로 렌더링하는 방법이다.


[SPA 참고자료](https://www.excellentwebworld.com/what-is-a-single-page-application)

# 기존 어플리케이션과 SPA의 차이

- 기존 어플리케이션은 화면 이동시에 필요한 HTML을 서버사이드에 받아서 다시 로딩한다.(시간이 오래 걸림)
- 이는 각 `url`로 `HTML` 요청을 보내면 새로운 `HTML`을 클라이언트에게 내려 줬습니다.
- SPA에서는 화면 구성에 필요한 HTML을 클라이언트가 가지고 있다.
- 서버사이드에서는 필요한 데이터들을 요청하며 JSON으로 받는다. 
- `url`에 따라서 필요한 부분만 업데이트 해준다.
- 그렇기 때문에 기존의 어플리케이션에 비해서 화면을 구성하는 속도가 빠르다.

```
 <클라이언트>

- HTTP 요청을 보내는 주체로 유저가 사용하는 브라우저
```

```
 <서버>

- 브라우저에서 요청한 데이터를 내려주는 주체 우리가 정한 규칙대로 행동하는 컴퓨터
```

## Component 기반의 UI

- 컴포넌트는 우리가 필요로 하는 `Component`를 만들고, 이 `Component`를 필요로 하는 곳에서 원하는 대로 사용한다.
- 이를 재사용성이 좋은 컴포넌트라 한다.

# 모든 플랫폼에서 사용 가능하다.

- `React`는 웹개발 이외에도 `React Native`를 통해서도 앱을 만들 수 있습니다.
- `Electron`을 통해서 PC앱도 만들 수 있습니다.

## 서버사이드 렌더링(SSR)과 클라이언트 사이드 렌더링(CSR)의 차이

# SSR

- 서버사이드 렌더링(SSR)은 `페이지를 이동할 때마다 새로운 페이지를 요청`한다.
- 모든 탬플릿은 `서버 연산을 통해서 렌더링`되고 완성된 `페이지 형태로 응답`한다.
- 이전의  SSR(Server Side Rendering)를 사용할 때에는 변화가 있을때마다 새롭게 전체 페이지를 로드해야 했다.
- ajax가 나온 이후에는 바뀐 부분만 특정해서 변화시킬 수 있지만 동작 하나하나를 지정하기 쉽지 않았다.
- 서버에서 HTML,CSS,JS 파일을 렌더링 하는 방식은 안드로이드와 IOS와 같은 다른 플랫폼과 서버를 공유할 수 없어서 비효율적이었다.

# CSR

- SSR과 달리 `서버로부터 데이터를 받아서 클라이언트에 렌더링`하는 방식이다.
- 서버로부터 `데이터를 받아서 바뀐 부분의 데이터가 있는 화면만 새롭게 렌더링`한다.
- 사용자 `경험을 높여주면서 효율적으로 클라이언트 리소스를 사용`하게 한다.
- 서버가 HTML,CSS,JS 파일을 렌더링하는 대신 데이터를 보내주는 경우에는 안드로이드,IOS,웹 모바일 등 다양한 플랫폼이 서버를 공유할 수 있게 한다.
- 이를 통해 효율적으로 서버를 운영할 수 있게 한다.
    - CSR은 검색엔진 SEO 최적화를 할 수 없는 문제가 있다. 하지만 `리액트에서는 CSR과 SSR을 함께 사용`할 수 있다.

## Create React App 이란?

- `Create React App(CRA)`는 리액트를 쉽게 사용할 수 있도록 만드는 도구이다.
- 이를 사용하게 되면 누구나 손쉽게 React를 사용할 수 있게 만들어준다.

# create-react-app으로 시작하기

- create-react-app은 리액트로 웹 애플리케이션을 만들기 위한 환경을 제공한다.
- create-react-app을 이용하게 된다고 한다면 기존 기능을 개선하거나 또는 새로운 기능을 추가했을 때 패키지 버전을 올리면 된다.

# Node.js 설치하기


- Node.js LTS를 설치합니다.
```
<Node.js>를 설치해야 하는 이유
- javascript로 작성된 라이브러리를 설치할 때 NPM을 사용하기 위해서입니다. 
```
# '12.18.3 LTS' 와 '14.9.0 Current'의 차이점

- `Node.js`를 만드는 개발자들은 **4월에서 10월**에 메이저 버전의 `Node.js` 를 출시합니다.
- 짝수 메이저 (4월) / 홀수 메이저 (10월)→ 출시 후 **Current**
- 4월에 출시된 짝수 메이저 버전 Node.js→ 6개월간 **Current→**10월에 **LTS버전**

# 10월

- 같은 해 4월에 출시되니 버전이 LTS버전이 됨
- 홀수 메이저 버전인  `Node.js`가 새로 출시

# LTS버전이란?

- **'Long Term Support' 줄임말 →`Node.js`** 커뮤니티에 의해 장기간 지원받을 수 있는 버전이란 뜻
- 30개월동안은 해당 버전에서 발견되는 각종 버그 등에 대한 패치(수정)를

**`Node.js`** 커뮤니티로부터 보장받을 수 있음

- 이는 **프로그램의 안정성** 등에 집중하여 실제 **서비스용으로 배포**하기 위한 용도
- 실 서비스에서 **`Node.js` 사용 시 LTS 버전을 사용해야 함**

# Current 버전이란?

- 안정성보다는 **새로운 기능 추가 등에 집중**하기 위한 **용도로 관리**되는 버전

## 첫 React Project 만들어 보기

```
# react 프로젝트 초기하기
npx create-react-app my-app

# 폴더 이동하기
cd my-app

# react 실행하기
npm start

```

```
<npx>
- `npx`는 `npm`의 확장 버전으로 `npm`이 하지 못했던 일들을 추가적으로 할 수 있게 도와줍니다.
- `npx`는 `npm`이 설치되면 자동으로 설치가 됩니다.
```
## npm (node package manager)

- npm은 자바스크립트 노드에서 `node package manager` 약자이다.
- 런타임 동안 `Node.js` 환경에서 다른 종류의 `패키지를 관리`하는데 사용되는 기본 절차이다.
- npm은 커맨드 라인 클라이언트에 의존하며 공개 패키지와 지블방식의 개인 패이지의 온라인 데이터베이스로 이루어져 있다.
- 이 레지스트리는 클라이언트를 통해서 접근이 되며 사용 가능한 패키지들은 npm 웹사이트를 통해서 찾아보고 검색할 수 있다.
- 패키지 관리자와 레지스트리는 npm사에 의해 관리된다.

## Yarn

- npm이 자바스크립트 커뮤니티에서 사용이 된다고 해도 단점이 있다.
- 이런 이유로 npm 핵심 이슈를 해결하기 위해 새로운 패키지 매니저들을 만들었다.
- 이것이 바로 얀(Yarn)이다.
- 빠르고 안정적이며 보안성이 뛰어나다고 주장하다며 등장하였다.

## Component?

- 컴포넌트란 `독립적이고 재사용이 가능하도록 나눈 조각`을 말합니다.

## 리액트에서 컴포넌트 만들기

```
// App.js
import React from "react";
import "./App.css";

function App() {
    return <div>Hello React!</div>;
}

export default App;
```


터미널에서 `npm start`로 `React`코드를 실행시키면 된다.
이 코드는 `App` 함수는 `React`에서 사용하는 하나의 큰 컴포넌트이다.
`return`에서 우리가 화면에 그리고자 하는 `HTML`을 작성하면 `react`가 `HTML`을 인식하고 브라우저에 그리게 된다.

```
import React from "react";
import "./App.css";

function App() {
    return (
        <div>
            <div>Hello React!</div>
            <button>button</button>
            // 버튼 추가
        </div>
    );
}

export default App;
```

## JSX가 무엇일까요?

```
function App() {
    return <div>Hello React!</div>;
}
```
 - 여기서 `<div>Hello React!</div>;`는 HTML을 다루는 방식으로 JSX라고 부릅니다.
 - JSX는 `React에서 HTML을 쉽게 사용할 수 있는 문법`이라고 보면 된다. 
 - 브라우저는 `HTML,CSS,Javascript`만 이해할 수 있지만 어떻게 저런 문법을 사용할 수 있는것일까?
 - 그것은 `create react app`으로 `React`앱을 최초 생성할 때 설치되는 라이브러리 `babel`이 `JSX`라는 문법을 이해할 수 있게 해줍니다.
 - 이는 `JSX`로 HTML을 작성하면 React 파일을 실행하게 될 떄 `babel`이 자동으로 JSX를 코드를 브라우저가 이해할 수 있는<br> 
`Javascript 코드로 변환`해준다.


Javascript 함수처럼 생겼는데 이러한 역할을 babel이 합니다.

## JSX로 만든 컴포넌트는 어디에 그려질까?

```
// index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));
```

- index.js에 있는 코드들 중 `ReactDOM.render`함수의 역할은 정해진 위치에 원하는 컴포넌트를 렌더링 하는 것(HTML에 내가 원하는 컴포넌트를 표현)
- 이 코드에서 첫번째 인자로 `Component`를 받고 두 번째 인자로는 실제로 `Component`를 그릴 위치를 받습니다.
    1. `html`에서 id가 root인 엘리먼트를 찾는다. `document.getElementById("root"))`
    2. 찾은 엘리먼트에 `<App />`컴포넌트를 렌더링 한다.
- html 파일은 `create-react-app`을 통해서 `React` 프로젝트를 만들면 `public`폴더가 생기는데 그 안에 `index.html`이 있습니다.

```
// public/index.html
<!DOCTYPE html>
<html lang="en">
    <head>
        ...
    </head>
    <body>
        ...
        <div id="root"></div>
        ...
    </body>
</html>
```
- `id`가 `root`인 `div` 태그를 찾을 수 있는데 이 곳이 `React`에서 `Component`를 최초 렌더링 하는 곳입니다.
- `document.getElementById('root')` 에서 root1로 바꾸면 에러가 뜬다.

## JSX 없이 사용하는 React

- 각 JSX 엘리먼트는 `React.createElement(component, props, ...children)`를 호출하기 위한 문법입니다. 
- JSX로 할 수 있는 모든 것들은 순수 JavaScript로도 할 수 있습니다.

```
// JSX로 작성된 코드
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

ReactDOM.render(
  <Hello toWhat="World" />,
  document.getElementById('root')
);
```

```
// JSX를 사용하지 않은 문법
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

ReactDOM.render(
  React.createElement(Hello, {toWhat: 'World'}, null),
  document.getElementById('root')
);
```
- 컴포넌트는 문자열이나 `React.Component`의 하위 클래스 또는 컴포넌트를 위한 일반 함수로 제공됩니다.

```
// 짧은 변수에 할당하는 방법
const e = React.createElement;

ReactDOM.render(
  e('div', null, 'Hello World'),
  document.getElementById('root')
);
```
- 이렇게 짧은 변수에 할당하게 되면 편리하게 JSX없이 React를 사용할 수 있습니다.

## 유용한 JSX 문법 알아보기

# JSX에서 표현식 사용하기 

- JSX는 중괄호 안에 유효한 모든 javascript 표현식을 넣을 수 있습니다.

# 표현식(expressions)과 문장 (statements)

- 표현식은 `값을 산출하는 코드의 조각`들을 일컫습니다.

# 표현식(expressions)

```
// 숫자 표현식
10;
10 + 20;

// 문자 표현식
"hello";
"hello" + "world";

// 논리 표현식
30 > 20;
10 < 20;

// 할당 연산자를 통한 표현식
i = 10;
total = 0;
fruits[1] = "avocado";

// 함수 호출 표현식
sayHello();
```
# 문장(statements)

```
// 변수 선언
var sum;
var average;

// if, else 문
if (expression)
    statement 1
else
    statement 2

// for 문
for(let i = 0; i < 10; i++){
    console.log(i)
}
```

# JSX에서 변수 넣기

 - 변수를 JSX 내부에 넣어보도록 하겠습니다.
 
```
const name = "code pot";
const App = () => {
    return <div>{name}</div>;
    // 선언한 변수를 중괄호로 감싸 JSX에서 사용할 수 있습니다.
};
```

# JSX에서 함수 사용하기

```
const user = {
  firstName: 'toy',
  lastName: 'crane'
};

const formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const App = () => {
    return <div>{formatName(user)}</div>
    // user 함수를 사용함
}
```
# JSX에서 if문처럼 사용하기

- JSX내에서는 표현식만 사용할 수 있습니다. 그래서 if문과 같은 효과를 내기 위해서는 삼항 연산자를 사용해야 합니다.

```
const isLogin = true;
const App = () => {
    // isLogin이 True이면 첫번째 요소인 <div>로그인 완료</div>의 표현식을 실행
    // isLogin이 False이면 두번째 요소인 <div>로그인 해주세요</div> 표현식을 실행
    return {isLogin ? <div>로그인 완료</div> : <div>로그인 해주세요</div>}
```

# JSX에서 for문처럼 사용하기

- if문과 마찬가지로 for문도 문장이기 때문에 사용할 수 없으므로 map을 사용한다.

```
const fruits = ["사과", "감", "배", "딸기"];
const App = () => {
    // fruits를 한바퀴 씩 돌면서 fruits가 끝날 때까지
    // map 안에 있는 함수((fruit) => <div>{fruit}</div>)를 실행함
    // 함수 안에 있는 fruit은 fruits 리스트의 각 요소입니다.
    return fruits.map((fruit) => <div>{fruit}</div>);
};
```

## JSX에서 주의해야 할것

1. React import 하기

- JSX를 `babel`이 이해하고, `Javascript`로 변환하기 위해서는 `import React from "react"` 를 해주어야 한다.
※ React v17 이후는 업데이트가 되어서 자동으로 JSX를 javascript로 변환한다.

2. HTML처럼 태그를 반드시 닫아야 한다.

```
function App() {
  return (
    <div>Hello React!
  );
}
```
- 이런식으로 닫지 않으면 컴파일 에러가 발생합니다.

3. HTML과 달리 반드시 최상위 태그(부모 태그)가 있어야 한다.

```
import React from "react";

function App() {
    return (
        // 컴포넌트 최상위 부모 태그
        <div>
            <div>Hello React!</div>
            <button>button</button>
        </div>
    );
}

export default App;
```
- 최상위 태그를 넣지 않으면 `tag`로 감싸달라고 하면서 `컴파일 에러가 발생`합니다.
- `div`를 최상위 태그로 넣기 싫다면 `<> </> fragment`를 사용하는 것이 좋다.
- `fragment`는 브라우저에 나타나지 않는 태그로 `element`들을 감쌀 때 사용합니다.

```
import React from "react";

function App() {
    return (
        // 컴포넌트 최상위 부모 태그 (fragment 사용)
        <>
            <div>Hello React!</div>
            <button>button</button>
        </>
    );
}

export default App;
```

## var, let, const 차이점

# 변수 선언 방식

- `var`는 변수 선언 방식에 있어서 큰 단점을 가지고 있다.

```
    var name = 'bathingape'
    console.log(name) // bathingape

    var name = 'javascript'
    console.log(name) // javascript
```
- 변수를 한번 더 선언했음에도 불구하고 에러가 나오지 않고 다른 값이 출력된다.
- 유연한 변수 선언으로 테스트 할때는 용이하나 코드량이 많아지면 어디서 사용해야 할지 판단도 어렵고 값이 바뀔 우려가 있다.

**ES6 이후 이를 보완하기 위해 추가한 변수 선언 방식이 let과 const이다.**

```
    let name = 'bathingape'
    console.log(name) // bathingape

    let name = 'javascript'
    console.log(name) 
    // Uncaught SyntaxError: Identifier 'name' has already been declared
```

```
    const name = 'bathingape'
    console.log(name) // bathingape

    const name = 'javascript'
    console.log(name) 
    // Uncaught SyntaxError: Identifier 'name' has already been declared
```

- `name`이 선언되었다는 에러 메세지가 나온다.

# let과 const의 차이점

- `let`은 변수에 재할당이 가능하다.

```
 let name = 'bathingape'
 console.log(name) // bathingape

 let name = 'javascript'
 console.log(name) 
 // Uncaught SyntaxError: Identifier 'name' has already been declared

 name = 'react'
 console.log(name) //react

```

- `const`는 변수 재선헌, 변수 재할당 모두 불가능하다.

```
 const name = 'bathingape'
 console.log(name) // bathingape

 const name = 'javascript'
 console.log(name) 
 // Uncaught SyntaxError: Identifier 'name' has already been declared

 name = 'react'
 console.log(name) 
 //Uncaught TypeError: Assignment to constant variable.

```

## 호이스팅

- 호이스팅(Hoisting)이란 var선언문이나 function 선언문 등을 해당 스코프의 선두로 옮긴것처럼 동작하는 특성을 말한다.
- 자바스크립트는 ES6에서 도입된 let,const를 포함하여 모든 선언 (var,let,const,function,function*, class)를 호이스팅한다.
※ var로 선언된 변수와 달리 let으로 선언된 변수를 선언문 이전에 참조하면 참조에러가 발생한다.

```
	console.log(foo); // undefined
	var foo;

	console.log(bar); // Error: Uncaught ReferenceError: bar is not defined
	let bar;
```

- let으로 선언된 변수는 `스코프의 시작에서 변수의 선언까지 일시적 사각지대(Temporal Dead Zone; TDZ)`에 빠지기 때문이다.
- var로 선언된 변수는 선언 단계와 초기화 단계가 한꺼번에 이루어진다.

```
// 스코프의 선두에서 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.

console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

- `let`으로 선언된 변수는 `선언 단계와 초기화 단계가 분리`되어 진행된다.

```
// 스코프의 선두에서 선언 단계가 실행된다.
// 아직 변수가 초기화(메모리 공간 확보와 undefined로 초기화)되지 않았다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 없다.

console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

## 정리

- 변수 선언에는 기본적으로 `const`를 사용하고 재할당이 필요한 경우에는 `let`을 사용한다.
- 객체를 재할당하는 경우는 흔하지 않다. `const`를 사용하면 의도치 않은 재할당을 방지하기 때문에 안전하다.
1. 재할당이 필요한 경우 `let`을 사용하되 변수의 스코프는 최대한 좁게 만든다.
2. 재할당이 필요없는 상수와 객체에는 `const`를 사용한다.

## 자바스크립트 값, 표현식,구문,리터럴 의미 설명

# 값이란?

- 값이란 표현식의 결과이다. 
```
1
30 + 10
```
- 값은 표헌식을 `실행`한 결과이며 실행을 거치고 난 값이 특정한 메모리에 할당이 된다.

# 표현식이란?

- 표현식이란 값을 산출하는 구문이다.

```
1;
30 + 10;
'Hello';
plus(); // 함수가 이미 선언되어 있다고 가정한다.
```
- 값과 다를바가 없어보이지만 실제로는 다르다.
- 여기서 `1;`은 그냥 숫자 1에 불과할 수 있지만 자바스크립트에서는 숫자 리터럴을 통해서 작성된 표현식을 자바스크립트 엔진에 실행후 값을 출력한다.

# 리터럴이란?

- 리터럴은 값을 표기하는 약속된 기호,문자,방법이다.
- 리터럴은 커뮤니케이션의 방식을 특정하는 일종의 방법이다.
- 사람이 이해할 수 있는 문자 또는 사전 정의된 기호를 사용해서 값을 생성하는 방법이 리터럴이다.

```
1; // 정수 리터럴
'Hello'; // 문자열 리터럴
[ 1, 2, 3 ]; // 배열 리터럴
{ age: 31 }; // 객체 리터럴
function() {} // 함수 리터럴
```

# 구문이란?

- 프로그램을 구성하는 기본 단위를 의미한다. 
- 구문이란 리터럴을 사용해 표현식을 작성하고 값을 처리하기도 하는 명령문을 의미한다.

```
var greeting = "Hello"; // 변수 선언문
function foo () {}; // 함수 선언문
if ( x = 5 ) {console.log(x);} // 조건문
for (var i = 0; i < 10; i ++) {console.log(i)}; // 반복문 
```

## 퀴즈 컴포넌트 만들기

# App 컴포넌트 뼈대 만들기

```
// App.js
import React from "react";

function App() {
    return (
        <div>
            <div>
                <div>
                    <h1>1/4</h1>
                    <div>일론 머스크의 우주 탐사 기업 이름은?</div>
                </div>
                <div>
                    <button>스페이스 엑스</button>
                    <button>테슬라</button>
                    <button>보링 컴퍼니</button>
                    <button>솔라시티</button>
                </div>
            </div>
        </div>
    );
}

export default App;
```
- 아무런 스타일이 없는 `HTML`이 나오게 됩니다.

# App Component에 Class 추가하기

- 색일 입히기 위해서는 HTML에 `선택자`를 넣어주어야 합니다.

```
import React from "react";

function App() {
    return (
        <div className="container">
            <div className="app">
                <div className="question-section">
                    <h1 className="question-header">
                        <span>1</span>/4
                    </h1>
                    <div className="question-text">
                        일론 머스크의 우주 탐사 기업 이름은?
                    </div>
                </div>
                <div className="answer-section">
                    <button>스페이스 엑스</button>
                    <button>테슬라</button>
                    <button>보링 컴퍼니</button>
                    <button>솔라시티</button>
                </div>
            </div>
        </div>
    );
}

export default App;
```
- `class` 이름을 `question-sction` 과 `answer-section` 로 주어 크게 두 영역으로 나눴습니다.
- className이 container인 친구로 전체 컴포넌트를 감싸주었습니다.

```
className은 HTML에서 사용하는 class와 같습니다. 리액트는 HTML 속성으로 -를 사용할 수 없기 때문에,
CAMEL CASE로 속성 값을 작성합니다.

```

# App Component에 CSS 입히기

```
// App.css
body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto",
        "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans",
        "Helvetica Neue", sans-serif;
}

.container {
    display: flex;
    justify-content: center;
    align-items: center;
}

.app {
    width: 400px;
    margin-top: 72px;
}

.question-section {
    margin-bottom: 16px;
}

.question-header {
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 8px;
}

.question-text {
    font-size: 20px;
    margin-bottom: 8px;
}

.answer-section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-bottom: 8px;
}

button {
    font-size: 16px;
    color: #ffffff;
    background-color: rgb(115, 98, 255);
    border-radius: 5px;
    border: 0px;
    height: 56px;
    padding: 4px;
    margin: 4px;
    cursor: pointer;
    width: 100%;
    outline: none;
    font-weight: 700;
}

button:hover {
    background-color: #a99fee;
}

```
- App 컴포넌트에서 해당 CSS를 불러오는 방법은 다음과 같습니다.
```
// app.js
import React from "react";
import "./App.css";

...

```

## 정답 오답 기능 추가하기 (React에서 event를 다루는 방법)

```
<EVENT는 무엇인가요?>
- 브라우저에서 사건이란 유저가 행하는 행동 (버튼 클릭, 필드에 값을 입력, 스크롤)
- 브라우저에서 일어나는 일 (HTML이 전부 rendering 되었을 때)들을 의미합니다.
```

# HTML에서 Event 처리하기

```
<button onclick="sayHello()">안녕하세요</button>
```
- `HTML`에서 `click event`를 다룰 때, 소문자 `onclick` 이벤트에 함수를 ""로 감싸서 문자열 형태로 실행

# React에서 Event 처리하기

```
<button onClick={sayHello}>안녕하세요</button>
```
- `React`에서는 HTML과 달리 `이벤트 명에 캐멀케이스를 사용`해야 하며 `함수 자체를 이벤트 핸들러에게 전달`해야 합니다.

# 퀴즈에 이벤트 연결하기

```
// App.js
import React from "react";
import "./App.css";


function App() {
    // handleClick 함수를 만들기
    const handleClick = () => {
        alert("버튼 클릭");
    };

    return (
        <div className="container">
            <div className="app">
                <div className="question-section">
                    <h1 className="question-header">
                        <span>1</span>/4
                    </h1>
                    <div className="question-text">
                        일론 머스크의 우주 탐사 기업 이름은?
                    </div>
                </div>
                <div className="answer-section">
                // handleClick 함수를 button에 연결하기
                    <button onClick={handleClick}>스페이스 엑스</button>
                    <button onClick={handleClick}>테슬라</button>
                    <button onClick={handleClick}>보링 컴퍼니</button>
                    <button onClick={handleClick}>솔라시티</button>
                </div>
            </div>
        </div>
    );
}

export default App;
```

## 발생한 이벤트의 값 가져오기

- 이벤트가 발생을 감지해 원하는 함수 (handleClick)를 실행하였습니다.
- 유저가 클릭한 버튼이 어떤 것인지 모르기 때문에 정답인지 오답인지 알 수 없습니다.

# button에 value값 넣어주기

- `value`값은 `event` 객체에서 접근이 가능하기 때문에 `value`값을 추가합니다.

```
//app.js
...
<button onClick={handleClick} value="스페이스 엑스">
    스페이스 엑스
</button>
<button onClick={handleClick} value="테슬라">
    테슬라
</button>
<button onClick={handleClick} value="보링 컴퍼니">
    보링 컴퍼니
</button>
<button onClick={handleClick} value="솔라시티">
    솔라시티
</button>
...

```

# 이벤트 핸들링 함수에서 event 받기

```
// app.js
...
const handleClick = (e) => {
        alert('버튼 클릭');
    }
...
<button onClick={handleClick} value="스페이스 엑스">
    스페이스 엑스
</button>
...
```
- React에서는 이벤트 핸들러 (onClike,onSubmit...)에 함수를 연결하면 함수의 첫번째 인자로 event객체를 전달합니다.

```
// app.js
import React from "react";
import "./App.css";

// 이벤트 핸들링 함수 실행
function App() {
    const handleClick = (e) => {
        console.log(e);
        alert("버튼 클릭");
    };

    return (
        <div className="container">
            <div className="app">
                <div className="question-section">
                    <h1 className="question-header">
                        <span>1</span>/4
                    </h1>
                    <div className="question-text">
                        일론 머스크의 우주 탐사 기업 이름은?
                    </div>
                </div>
                <div className="answer-section">
                    <button onClick={handleClick} value="스페이스 엑스">
                        스페이스 엑스
                    </button>
                    <button onClick={handleClick} value="테슬라">
                        테슬라
                    </button>
                    <button onClick={handleClick} value="보링 컴퍼니">
                        보링 컴퍼니
                    </button>
                    <button onClick={handleClick} value="솔라시티">
                        솔라시티
                    </button>
                </div>
            </div>
        </div>
    );
}

export default App;
```
- 코드를 실행하고 콘솔창에 찍힌 것을 보면 엄청 많은 정보를 담은 `event` 객체가 출력되는 것을 볼 수 있습니다.
- `event`객체 내의 `target`에 접근하여 실제 값을 가져와보도록 하겠습니다.

```
//app.js
...
const handleClick = (e) => {
    // target 출력
        console.log(e.target);
    };
...
```
- 여기서 필요한 값은 value이기 때문에 value값만 뽑아 보도록 하겠습니다.

```
//app.js
...
const handleClick = (e) => {
    // value 값 출력
        console.log(e.target.value);
    };
...
```

# 전체 코드

```
// app.js
import React from "react";
import "./App.css";

function App() {
    const handleClick = (e) => {
        console.log(e.target.value);
    };

    return (
        <div className="container">
            <div className="app">
                <div className="question-section">
                    <h1 className="question-header">
                        <span>1</span>/4
                    </h1>
                    <div className="question-text">
                        일론 머스크의 우주 탐사 기업 이름은?
                    </div>
                </div>
                <div className="answer-section">
                    <button onClick={handleClick} value="스페이스 엑스">
                        스페이스 엑스
                    </button>
                    <button onClick={handleClick} value="테슬라">
                        테슬라
                    </button>
                    <button onClick={handleClick} value="보링 컴퍼니">
                        보링 컴퍼니
                    </button>
                    <button onClick={handleClick} value="솔라시티">
                        솔라시티
                    </button>
                </div>
            </div>
        </div>
    );
}

export default App;
```

## 정답 오답 체크하기

- 버튼의 값을 answer변수에 받고 if문으로 정답일 때와 오답일 때를 분리하여 처리할 것

```
//app.js
const handleClick = (e) => {
    const answer = e.target.value;
    if (answer === "스페이스 엑스") {
        alert("정답 👏👏👏");
    } else {
        alert("오답 😣😣😣");
    }
};
```

## event란?

- 어떤 사건을 의미한다. 브라우저에서 사건이란 사용자가 클릭을 했을 때 스크롤을 했을 때 필드의 내용을 바꾸었을 때와 같은것이다.

# event target

- target은 이벤트가 일어날 객체를 의미한다. (ex.버튼을 누르면 새로운 창이 열리는 것)

# event type

- 이벤트의 종류를 의미한다. (click,scroll,MouseOver...)

# event handler

- 이벤트가 발생했을 때 동작하는 코드를 의미한다.

## 이벤트 등록방법

# inline

- 인라인 (inline) 방식은 이벤트를 이벤트 대상의 태그 속성으로 지정하는 것이다. 

```
<input type="button" onclick="alert('Hello world');" value="button" />
```

- 이벤트가 발생한 대상을 필요로 하는 경우 this를 통해서 참조할 수 있다.

```
// 자기 자신을 참조하는 불편한 방법
<input type="button" id="target" onclick="alert('Hello world,' + document.getElementById('target').value);" value="button" />

// this를 통해서 간편하게 참조할 수 있다
<input type="button" onclick="alert('Hello world,'+ this.value);" value="button" />

```
# property listener

```
<input type="button" id="target" value="button" />
<script>
    var t = document.getElementById('target');
    t.onclick = function(){
        alert('Hello world');
    }
</script>
```

- 프로퍼티 리스너 방식은 이벤트 대상에 해당하는 객체의 프로퍼티다.
- 이벤트를 등록하는 방식으로 인라인 방식에 비해서 HTML과 JavaScript를 분리할 수 있다는 점에서 선호된다.

# 이벤트 객체

- 이벤트가 실행한 맥락의 정보가 필요한 경우 이벤트 객체를 사용한다. 
- 이벤트 객체는 이벤트가 실행될 때 이벤트 핸들러의 인자로 전달한다.

```
<body>
    <input type="button" id="target" value="button" />
<script>
    var t = document.getElementById('target');
    t.onclick = function(event){
        alert('Hello world, '+event.target.value)
    }
</script>
```
이 방식은  **internet explore8 이하에서는 동작하지 않는다.**

# addEventListener()

- addEventListener는 이벤트를 등록하는 `권장하는 방식`이다.
- 이 방식을 사용하면 여러 개의 이벤트 핸들러를 등록할 수 있다.
※이 방식은  **internet explore8 이하에서는 호환하지 않는다.**

```
<input type="button" id="target" value="button" />
<script>
    var t = document.getElementById('target');
    //  getElementById로 html에서 target이라는 id를 갖고 있는 객체를 t변수에 리턴
    t.addEventListener('click', function(event){
        //  t는 addEventListener()를 호출하고 첫 번째 인자로 event type을 받는다.
        alert('Hello world, '+event.target.value);
        // 첫 번째 인자의 이벤트가 수행되었을 때 두 번째 인자로 받은 함수가 호출되면서 
        // 함수 내에 있는 코드가 실행된다.
    });
</script>
```

## Javascript '=='와 '===' 연산자 차이

# '=='와 '==='의 차이

- `==` 연산자는 **동등 연산자**로 서로 다른 타입이면 타입을 강제로 변환하여 비교한다.

```
0 == ''     //true
0 == '0'     //true
1 == true     //true
false == '0'    //true
null == undefined    //true
false == null    //false
false == undefined    //false
```
- `===`연산자는 **일치 연산자**로, 두 피연산자를 더 정확하게 비교한다.

```
0 === ''     //false
0 === false    //false
1 === true     //false
NaN === NaN     //false
null === undefined     //false
```

- 그러므로 특별한 경우가 아니라면 `===`를 쓰는것이 좋다.
