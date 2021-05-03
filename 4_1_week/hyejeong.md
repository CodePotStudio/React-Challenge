# 이번주 한 일
https://react.codepot.kr/ 을 따라하며 React의 전체적인 구조를 파악했습니다.

## React란?
페이스북에서 만든 Javascript 라이브러리입니다. MVC 패턴에서 View, 사용자가 조작하기 위한 UI를 만드는 것을 도와주는 역할을 한다.
### JSX 문법 사용
JSX는 자바스크립트 안에서 HTML문법을 사용해서 View를 구성할 수 있게 도와주는 자바스크립트 문법이다.
```
export Hello extends React.Component {
    render() {
        return {
            <div>
                Hello {this.props.name}
            </div>
        };
    }
}
```
### Component 기반
하나의 HTML 코드를 작성하는 것이 아니라, 여러 부분을 분할해서 **코드의 재사용성**과 **유지보수성**을 증가시킨다.
### Virtual DOM
기존의 DOM은 DOM이 수정되면 Render Tree 생성, Layout 생성, Paint가 다시 이루어진다. 이러한 문제를 해결하기 위하여 Virtual DOM이 탄생되었다.
간단하게 Virtual DOM을 설명하자면, 로컬에 Virtual DOM을 생성 시키고, 브라우저에 Render를 요청하기 전에 변경 사항을 요청하는 방식이다.
자세한 설명은 하단 링크로 대체한다. 아래 글들을 통해 Virtual DOM에 대해 이해할 수 있었다.
+ https://berkbach.com/%EA%B8%B0%EC%B4%88%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-react-js-1531b18f7bb2
+ https://ryublock.tistory.com/41
+ https://velopert.com/3236
 
## Create React App
React를 사용하려면 여러 세팅들을 해주어야 하는데, Create React App을 사용하면 손쉽게 React를 사용할 수 있다.
### React 프로젝트 생성 및 실행
```
# react 프로젝트 생성
npx create-react-app (프로젝트명)

# 폴더 이동
cd (프로젝트명)

# react 실행
npm start
```

## 컴포넌트 
### 구조
```
import React from "react";
import "./App.css";

function App() {
    return <div>Hello React!</div>;
}

export default App;
```
`App` 함수는 React에서 사용하는 하나의 큰 컴포넌트이고, return 안에는 화면에 그리고자 하는 HTML를 작성해주면 된다.
위는 JSX 문법이며 `create react app`으로 React 앱을 최초 생성할 때 `babel`이 자동으로 자동으로 `Javascript` 코드로 변환해준다.
```
// index.js
ReactDOM.render(<App />, document.getElementById("root"));
```
index.js 코드를 보면, `ReactDOM.render`가 있는데, 이 함수의 역할은 정해진 위치에 원하는 컴포넌트를 렌더링하는 것이다.
첫번째 인자로 `Component`를 받고, 두번째 인자로는 실제로 `Component`를 그릴 위치를 받는다. 이 위치는 `public` 폴더 안에 `index.html`에 존재한다.
### JSX 문법
JSX는 중괄호 안에 유효한 모든 javascript 표현식을 넣을 수 있다.
#### 변수
```
const name = "code pot";
const App = () => {
    return <div>{name}</div>;
};
```
중괄호로 감싸 사용한다
#### 함수
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
}
```
역시 중괄호로 감싸 사용한다
#### if문처럼 사용
```
const isLogin = true;
const App = () => {
    return {isLogin ? <div>로그인 완료</div> : <div>로그인 해주세요</div>}
```
JSX에서는 표현식만 사용할 수 있어 if문은 사용할 수 없다. 그래서 if문과 같은 효과를 내려면 삼항연산자를 사용해야 한다.
#### for문처럼 사용
```
const fruits = ["사과", "감", "배", "딸기"];
const App = () => {
    return fruits.map((fruit) => <div>{fruit}</div>);
};
```
for문도 역시 사용할 수 없어 주로 map을 사용한다.
#### 주의사항
+ React import 해야 한다
+ HTML처럼 태그 반드시 닫아야 한다
+ 반드시 최상위 태그(부모 태그) 존재해야 한다
    - 컴포넌트 최상위 태그에 `<div>`나 `<>`를 사용해야 한다.
### CSS 사용법
HTML과 동일하기 class를 사용한다. 그러나 React는 `HTML` 속성으로 -를 사용할 수 없기 때문에 CAMEL CASE인 className을 사용하여 작성한다.
### Event
```
<button onClick={sayHello}>안녕하세요</button>
```
React에서는 이벤트명에 CAMEL CASE를 사용하여야 하며, 함수 자체를 이벤트 핸들러에 전달해야 한다.
#### 이벤트 핸들링 함수에서 event 받기
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
`React`에서 이벤트 핸들러에 함수를 연결하면 함수의 첫번째 인자로 `event` 객체를 전달해 준다.
#### 이벤트 핸들러에 매개변수 넘기기
```
// app.js
...
<button value={answer.text} onClick={() => handleClick(answer.isCorrect)}>
    {answer.text}
</button>
...
```
화살표로 한 번 더 감싸 주면 매개변수를 넘길 수 있다. 만약에 화살표 함수로 감싸지 않고 바로 넘겨주면, 컴포넌트를 그릴 때 바로 함수가 실행하게 된다. 그렇게 된다면 원할 때 함수가 실행되지 않고, 시작하자마자 끝나는 불상사가 일어날 수 있다.

## 참고 문서
+ https://react.codepot.kr/
+ https://berkbach.com/%EA%B8%B0%EC%B4%88%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-react-js-1531b18f7bb2
+ https://ryublock.tistory.com/41
+ https://velopert.com/3236

## 한주 후기
React를 처음 공부해보는데 JSX 문법이 색다른 거 같다. 아직까지는 html와 javascript가 혼용되는 코드가 익숙하지 않아 어려운 것 같다. state 부분까지 정리하려고 했으나, 막상 정리하려고 보니 쉽게 정리되지 않아 다시 공부해야 겠다는 생각이 들었다.
그리고 이러한 정리를 처음해보는데 어려운 것 같다. 계속 참고한 문서의 문장을 그대로 사용하게 된다랄까... 앞으로는 나만의 문장으로 바꿔서 작성해보는 버릇을 들어야겠다.

## 다음주 할 일
status 및 codepot 계속 학습하기