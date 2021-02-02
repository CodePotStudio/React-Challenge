# Components and Props/State

컴포넌트를 통해 UI 를 여러 조각으로 나누고 개발적으로 살펴볼 수 있다.

> 컴포넌트는 js 함수와 유사하다. props 라는 임의의 임력을 받고, 화면에 어떻게 표시되는지 기술하는 react 엘리먼트를 반환한다.



## 함수 컴포넌트와 클래스 컴포넌트

js 함수로 컴포넌트 정의 가능

```jsx
function welcome(props){
  return <h1>hello world</h1>;
}
```

es6 class를 사용해서 컴포넌트 정의 가능

```jsx
class welcome extends React.Component {
  render () {
    return <h1>hello, {this.porps.name}</h1>;
  }
}
```



## 컴포넌트 랜더링

이전까지 했던 방식

```jsx
const element = <div />;
```

컴포넌트 사용

```jsx
const element = <Welcome name="Sara "/>;
```

react가 컴포넌트로 작성한 엘리먼트를 보면 JSX 어트리뷰트와 자식을 해당 컴포넌트에 단일 객체로 전달한다. 이 객체를 `props` 라고 부른다.

```jsx
class Welcome extends React.Component {
  render () {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

const element = <Welcome name="Minsoo" />;
ReactDOM.render(
	element,
	document.getElementById('root')
)
```

> 컴포넌트 이름은 반드시 대문자로 작성해야한다.



## 컴포넌트 합성

```jsx
class Welcome extends React.Component {
  render () {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

class App extends React.Component {
  render () {
    return <div>
  <Welcome name='minsoo' />
  <Welcome name='jisoo' />
  <Welcome name='hansoo' />
  </div>;
  }
}

ReactDOM.render() {
  <App />
  document.getElementById('root')
}
```



## 컴포넌트 추출

여러 개의 작은 컴포넌트 조각으로 나눌 수도 있다.

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

/////////////////////////////////
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
      />
  )
}

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.author} />
      <div className="UserInfo-name">
        {props.author.name}
      </div>
    </div>
  )
}

function Comment(props) {
  return (
      <div className="Comment">
		<UserInfo author={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

## Props 는 읽기 전용이다

```jsx
// 순수함수
function sum(a,b) {
  return a+b;
}

// 매개변수로 들어온 값을 변경하기 때문에 순수함수가 아니다.
function draw(account, amount) {
  return account.total -= amount;
}
```

**React props는 반드시 순수 함수처럼 동작해야한다**

## State and Lifecycle

엘리먼트 렌더링에서는 UI를 업데이트하는 단 한가지 방법만 배웠다. 렌더링 된 값을 업데이트하기 위해 `ReactDOM.render()` 를 사용했다.

아래 시계 UI를 구현하는 코드를 다시 살펴보자.

```jsx
function tick() {
  const element = (
  	<div>
    	<h1>Hello</h1>
      	<h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}
setInterval(tick, 1000);
```



컴포넌트를 재사용하고 캡슐화 하는 법을 배워보자.

```jsx
function Clock(props) {
  return (
    <div>
      <h1>Hello</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  )
}

function tick() {
  ReactDOM.render(
  	<Clock data=Date()} />
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

Clock 컴포넌트가 타이머를 설정하고 매 초 UI를 업데이트하는 것이 Clock 내부 구현사항이 되어야한다.



```jsx
ReactDOM.render(
	<Clock />,
	document.getElementById('root')
)
```

이를 구현하기 위해서는 Clock 에 `state` 를 추가해야한다.

State는 props 와 유사하나, 비공개이며 컴포넌트에 의해 제어된다.



## 함수에서 클래스로 변환

Clock 컴포넌트를 class 컴포넌트로 변환하자.

```jsx
class Clock extends React.Component {
  render() {
    return {
      <div>
      	<h1>Hello, World!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}</h2>
      </div>
    }
  }
}
```

## 클래스에 로컬 state 추가하기

`date` 를 props 에서 `state` 로 이동해보자

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  render() {
    <div>
    	<h1>Hello,World</h1>
      	<h2>It is {this.state.date.toLocaleTimeStirng()}</h2>
    </div>
  }
}

ReactDOM.render(
	<Clock />,
  	document.getElementById('root')
);
```

## 클래스에 생명주기 메소드 추가

많은 컴포넌트가 존재하는 어플에서 컴포넌트가 삭제될 때 해당 컴포넌트가 사용 중이던 리소스를 확보하는게 중요하다.

- Clock 이 처음 DOM에 렌더링 될 때마다 `타이머를 설정`하려고 하는데, 이것을 **마운팅**이라고 한다.
- Clock 에 의해 생성된 DOM이 삭제될 때마다 `타이머를 해제` 하려고한다.

컴포넌트 클래스에서 특별한 메소드를 선언하여 컴포넌트가 마운트되거나 언마운트 될 때 일부 코드를 작동할 수 있다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    // Can't use 'this' yet
    super(props);
    // Now it's okay though 'this'
    this.state = {date: new Date()};
  }
  
  componentDidMount() {
    
  }
 
  componentWillUnmount() {}
  }
  
  render () {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```

`componentDidMount()` 메소드는 컴포넌트 출력물이 DOM에 렌더링 된 이후에 출력된다. 이 메소드에 타이머를 설정하는 기능을 추가하자.

```jsx
componentDidMount() {
  this.timerID = setInterval(
    () => this.tick(),
    1000
  );
}
```

`componentWillUnmount()` 를 이용해서 생명주기 안에 있는 타이머를 분해한다.

`this.setState()`를 로컬 state를 업데이트 하는데 사용한다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    // Can't use 'this' yet
    super(props);
    // Now it's okay though 'this'
    this.state = {date: new Date()};
  }
  
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
  	);
  }
 
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
  
  // Clock 컴포넌트가 매초 작동하도록 구현
  // 로컬 state를 업데이트하기 위해 this.setState() 사용
  tick() {
    this.setState({
      date: new Date()
    })
  }
  
  render () {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    )
  }
}
```



