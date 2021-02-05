# React

## JSX

React 에서는 이벤트 처리, 시간에 따라 state가 변하는 방식, 화면에 표시하기 위해 데이터가 준비되는 방식 등 렌더링 로직이 UI 로직과 연결된다는 사실을 받아들인다.

React는 **컴포넌트** 라는 개념을 이용해서 관심사를 분리한다. React에서 JSX가 필수는 아니지만, 더 도움이 된다.

JSX의 중괄호 안에는 모든 js표현식을 사용할 수 있다.

```jsx
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
```

### JSX도 표현식이다.

  컴파일이 끝나면, JSX 표현식은 정규 js 함수 호출이 되고 js 객체로 인식힌다.
  즉, JSX를 js 처럼 사용할 수 있다.

```jsx
  function getGreeting(user) {
      if(user) {
          return <h1>Hello, {formatName(user)}!</h1>;
      }
      return <h1>Hello, Stranger!</h1>;
  }
```

### JSX 속성 정의

속성에 따옴표를 이용해서 문자열을 정의할 수 있다. 또한 중괄호를 통해 js 표현식을 넣을 수도 있다.

```JSX
const element = <div tableIndex="0"></div>;
const element = <img src={user.avatarUrl}></img>;

const element = (
  <div>
  	<h1>Hello!</h1>
    <h2>Good to see you.</h2>
  </div>
);
```

### JSX는 주입 공격을 방지한다.

```jsx
const title = response.potentiallyMaliciousInput;
const element = <h1>{title}</h1>
```

> React DOM 은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프하므로, 명시적으로 작성되지 않은 내용은 주입되지 않는다. 모든 항목은 렌더링 되기 전에 문자열로 변환된다. 그러므로 XSS 공격을 방지할 수 있다.

### JSX는 객체를 표현한다.

Babel 은 JSX를 `React.createElement()` 호출로 컴파일한다.

```jsx
// 두 예시는 동일하다.
const element = (
  <h1 className="greeting">
  	Hello, World!
  </h1>
);

const element = React.createElement(
	'h1',
  {className: "greeting"},
  "Hello, World!"
);
```



## Element Rendering

**엘리먼트는 React 앱의 가장 작은 단위이다.** 

엘리먼트는 화면에 표시할 내용을 기술한다.

```jsx
const element = <h1>Hello, World!</h1>;
```

브라우저 DOM 엘리먼트 와 달리 React 엘리먼트는 일반 객체이며 쉽게 생성 가능하다.
React DOM은 React 엘리먼트하도록 DOM을 주기적으로 업데이트한다.

> 다음 개념으로 소개될 컴포넌트와 혼동하면 안된다. 엘리먼트는 컴포넌트의 구성요소이다.



### DOM에 엘리먼트 렌더링

```jsx
<div id="root"></div>
```

이 태그에 들어가는 모든 엘리먼트를 React DOM에서 관리하기 때문에 `root` DOM 노드라고 부른다.

React로 구현된 대부분의 어플리케이션들은 하나의 루트DOM이 있다.

React 엘리먼트를 루트 DOM 노드에 렌더링하려면 둘 다 `ReactDOM.rendor()` 로 전달하면 된다.

```jsx
const element = <h1>Hello, World!</h1>;
ReactDOM.render(element, document.getElementById('root'));
```



### 렌더링된 엘리먼트 업데이트하기

React 엘리먼트는 불변객체이기 때문에 생성한 이후에는 자식이나 속성을 변경할 수 없다. 
엘리먼트는 영화에서 하나의 프레임처럼 특점 시점의 UI를 보여줄 뿐이다.

> :warning: **주의** 대부분의 React 앱은 `ReactDOM.render()` 를 한번만 호출한다. 다음 섹션에서 이런 코드가 유상태 컴퓨넌트에 어떻게 캡슐화 되는지 설명한다.

### 변경된 부분만 업데이트하기

실제로 `ReactDOM.render()` 를 이용해서 매 초마다 UI 전체를 다시 그리도록 엘리먼트를 만들긴 했으나, ReactDOM은 변경된 텍스트 노드만 업데이트한다.



