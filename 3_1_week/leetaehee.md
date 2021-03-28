# 0. React 입문하기

> 아래 내용은 <[벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)>와 [<React 공식 문서>](https://ko.reactjs.org/docs/getting-started.html)를 읽고 실행하면서 정리한 자료입니다. 



# 1. React 란?

- React는 사용자 인터페이스(UI) 개발을 위해 페이스북에서 개발한 JS 라이브러리입니다. 

- React를 사용하면 컴포넌트와 JSX를 통해 사용자와 상호작용할 수 있는 웹 애플리케이션 UI를 손쉽게 구성할 수 있습니다. 

- 또한 React는 가상 DOM이라는 개념을 사용하기 때문에 기존에 브라우저가 DOM을 렌더링하여 웹 페이지를 구성하는 것보다 빠른 속도로 웹 페이지를 구성할 수 있습니다.   

# 2. React 작업 환경 구성

React 환경을 구성하기 위해서 다음 항목들의 설치가 필요합니다. 

- Node.js : JS가 브라우저 밖에서도 동작할 수 있게 하는 환경
- NPM : Node기반의 패키지를 관리하는 도구
- CRA(create-react-app) : React 개발환경을 세팅해주는 도구   

### CRA 설치
```
npx create-react-app 프로젝트명
```
# 3. JSX와 컴포넌트


## 3-1. JSX (JavaScript XML)란?
JSX는 JavaScript를 확장한 문법으로 JS에 마크업을 결합하여 사용하는 기법입니다. 마크업을 사용하기 때문에 단순한 템플릿 언어의 특징을 보유함과 동시에 JS 기반 문법이기 때문에 JS의 모든 기능을 사용할 수 있다는 특징을 가지고 있습니다. 

```
const element = <img src={user.avatarUrl}></img>;
```
> - 마크업 특징으로 &lt;img&gt;태그의 속성을 정의하는 모습을 볼 수 있다.
>
> - JS의 특징으로 `user.avatarUrl` 이라는 JS 표현식을 사용하는 모습을 볼 수 있다.


## 3-2. 컴포넌트란?
React에서 컴포넌트는 재사용 가능한 개별적인 UI 조각입니다. 컴포넌트는 함수 또는 클래스로 정의하여 사용할 수 있습니다.

### 함수 컴포넌트 정의
```
function Welcome() {
  return <h1>Hello, React</h1>;
}
```

### 클래스 컴포넌트 정의
```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, React</h1>;
  }
}
```

## 3-3. 렌더링
React에서 렌더링은 컴포넌트들을 조립하여 만든 엘리먼트를 통해 화면을 구성하는 과정을 의미합니다. React DOM은 렌더링한 해당 엘리먼트와 이전의 엘리먼트와 비교한 후 차이가 발생한 경우에만 DOM을 업데이트합니다.

### 엘리먼트 렌더링 과정
---
#### 1. 컴포넌트 선언
JSX 문법으로 Hello 컴포넌트를 선언한다.
``` 
function Hello() {
  return <div>Hello React</div>;
}
export default Hello;
```
   
#### 2. 엘리먼트 형성
정의된 컴포넌트를 호출하여 화면에 렌더링할 형태로 컴포넌트들을 구성해 엘리먼트를 형성한다.
```
import Hello from "./Hello";

function App() {
  return (<div><Hello /><Hello /><Hello /></div>);
}
export default App;
```
   
#### 3. 엘리먼트 렌더링
지정된 구역(root)에 엘리먼트를 렌더링한다.
```
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

# 4. props로 컴포넌트에 값 전달
HTML 태그의 속성처럼 컴포넌트에게 특정한 값이나 이벤트 함수 등을 전달해야할 때 `props`를 사용할 수 있습니다. 단, `props`는 읽기 전용이기 때문에 `props`를 수정해서는 안됩니다.

## 4-1. props 사용하기
컴포넌트에 속성과 속성값을 전달하면 컴포넌트는 `props`를 통해 전달받은 속성과 속성값을 사용할 수 있습니다.   

#### <App.js>
```
function App() {
  return (
      <Hello name="taehee lee" color="red" />
  );
}
```
#### <Hello.js>
```
function Hello(props) {
  return <div style={{ color: props.color }}>Hello, {props.name}</div>;
}
```

## 4-2. default props 사용하기
컴포넌트에 props로 값을 전달하지 않았을 때 기본적으로 사용할 값을 설정하려면 `defaultProps`를 사용합니다.
```
function Hello(props) {
  return <div style={{ color: props.color }}>Hello, {props.name}</div>;
}

Hello.defaultProps = {
  name: "No Name",
};
```

## 4-3. props.children 사용하기
컴포넌트 태그 사이에 넣은 자식 컴포넌트를 사용하기 위해서는 `props.children` 속성을 사용합니다.

#### <App.js>
```
function App() {
  return (
    <Wrapper>
      <Hello name="taehee lee" color="red" />
      <Hello color="blue" />
    </Wrapper>
  );
}
```
#### <Wrapper.js>
```
function Wrapper(props) {
  const style = {
    border: "2px solid black",
    padding: 16,
  };
  return <div style={style}>{props.children}</div>;
}
```
> `props.children` 속성을 사용하지 않으면 Wrapper 컴포넌트로 감싼 Hello 컴포넌트가 출력되지 않는다.

# 5. state와 useState
## 5-1. state
`state`는 컴포넌트 내에서 동적으로 변경되는 값을 저장하는 속성으로 클래스 컴포넌트에서만 `state` 속성을 사용할 수 있습니다. 
> - state : 컴포넌트 자신이 가지고 있는 동적으로 변할 수 있는 값
> - props : 부모 컴포넌트가 자식 컴포넌트에 전달하는 값(읽기 전용)

### state 설정
클래스 컴포넌트의 생성자에서 `state` 속성을 초기화합니다.
```
class Item extends Component {
  constructor(props){
    super(props);
    this.state = {
      stateProperty: initialValue;
    }
  }
  render(){
    return <div></div>
  }
}
```

### state 변경
`state` 속성은 직접적으로 `state` 속성값을 변경하지 않고 `setState` 함수를 통해 속성값을 변경합니다. 단, `setState`함수는 비동기적으로 처리되기 때문에 `state`가 변경된 후 리렌더링 하기 이전에 함수를 실행시키고 싶다면 `setState`함수 내에 실행할 `callback` 함수를 넣어주어야합니다. 만약 `state` 변경 후 `callback` 함수가 실행된다면 해당 함수 실행이 종료된 후 리렌더링됩니다. 
```
this.setState({stateProperty: changeValue}, [callback]);
```

## 5-2. useState
React v16.8부터 Hooks 기능이 도입되면서 함수형 컴포넌트에서도 `useState`라는 함수를 통해 `state`속성을 다룰 수 있게 되었습니다. `useState` 함수는 `state`속성과 `state`속성값을 변경할 수 있는 `setState` 콜백 함수를 배열로 묶어 리턴합니다. `useState`에서 `state`속성과 `setState`에 여러 속성을 object로 묶어 사용하면 여러 개의 `state`를 다룰 수 있습니다.

```
import React, { useState } from "react";

function Item(){
  const [stateProperty, setState] = useState(initialValue);

  setState(newState); // ex) value (count = count + 1)
  setState(prevState => prevState + value); // ex) callback ((prevCount) => prevCount + 1)

  return <div></div>;
}
```

# 6. 이벤트 처리하기
## 6-1. 이벤트 선언
React에서 이벤트를 처리하는 방법은 DOM 엘리먼트에서 이벤트를 처리하는 방법과 유사하지만 선언 방식에 약간의 차이가 있습니다.

- React는 이벤트 선언에 캐멀 케이스(camelCase)를 사용합니다.
- React는 문자열이 아닌 함수로 이벤트 핸들러를 전달합니다. (`addEventListener`를 호출할 필요가 없습니다.)
  
&lt;HTML 이벤트 선언>
```
<button onclick = "ClickHandler()"></button>
```

<React 이벤트 선언>
```
<button onClick = { clickHandler }></button>
```

## 6-2. 이벤트 핸들러의 this (함수 리터럴 vs 화살표 함수)
React의 클래스 컴포넌트에서 함수 리터럴 형태로 선언한 이벤트 핸들러를 `this`로 연결하려면 바인딩을 필수적으로 해주어야합니다. 바인딩을 하지 않았다면 `this`는 `undefined`가 됩니다. 이 문제는 함수 리터럴 형태가 아닌 바인딩을 자동으로 수행하는 화살표 함수 형태로 이벤트 핸들러를 설정하면 해결됩니다.

> 함수 clickHandler의 `this`는 익명 함수 function(){ }의 `this`가 된다. 하지만 JS엔진은 이 `this`를 판단하지 못해 undefined나 최상위 객체인 window로 간주한다.


```
class ClickButton extends React.Component{
  constructor(){
    // clickHandler의 this가 undefined이므로 ClickButton 객체를 바인딩하여 해결한다.
    // this.clickHandler = this.clickHandler.bind(this);
  }

  clickHandler(){
    console.log(this); // 결과 : undefined
    console.log("Hello React!");
  }
  const clickListener = () => {
    console.log(this); // 결과 : ClickButton
    console.log("Hello React!");
  }
  render(){
    return (
      <button onClick={this.clickHandler}>Function Literal</button>
      <button onClick={this.clickListener}>Arrow Function</button>
    )
  }

}
```

## 6-3. 이벤트 핸들러에 인자 전달하기
이벤트 핸들러에 매개변수를 전달하기 위해서는 다음 두 가지 방법을 사용할 수 있습니다. event 객체는 가장 마지막 인자로 전달되지만 바인딩할 경우에는 자동으로 전달됩니다.

```
<button onClick={this.clickHandler.bind(this, id)}>Function Literal</button>

<button onClick={(e) => this.clickListener(id, e)}>Arrow Function</button>
```