# 2주차: [Build your own React](https://pomb.us/build-your-own-react/) 을 학습하며



## Step 0: Basic Review

간단한 리액트 코드가 있습니다.

```jsx
const element = <h1 title="foo">Hello</h1>;	// 리액트 엘리먼트를 정의한다.
const container = document.getElementById("root");	// DOM으로부터 노드를 얻는다.
ReactDOM.render(element, container);	// 컨테이너 안에 리액트 엘리먼트를 생성한다.
```



간단한 3줄의 리액트 코드들을 모두 바닐라 자바스크립트로 교체해봅니다.

첫 줄에는 JSX로 정의된 엘리먼트가 있습니다.

```jsx
const element = <h1>Hello</h1>;
```

이는 자바스크립트에서 유효한 문법이 아니기때문에 다음과같이 교체해줍니다.

바벨과 같은 빌드 툴에의해서 위 코드는 아래처럼 자바스크립트 코드로 변환됩니다.

```javascript
const element = React.createElement(
	"h1",
  {
		title: "foo"
  },
  "Hello"
)
```

`React.createElement` 함수는 전달받은 아규먼트들로 객체를 생성하게됩니다.

`React.createElement` 함수를 실행한 결과는 다음과 같습니다.

```javascript
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello"
  }
}
```

`type` 과 `props` 를 객체 키 값으로 가지는 객체가 생성됩니다. (실제로는 더 많은 속성이 있다고 하네요.)

현재 결과물은 다음과 같습니다.

```jsx
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello"
  }
};
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

세번째 줄의 `ReactDOM.render(element, container);` 코드 또한 바닐라 자바스크립트로 바꿔봅니다.

우선 DOM 엘리먼트를 생성합니다.

```javascript
const node = document.createElement(element.type);	// "h1"
node["title"] = element.props.title;	// "foo"
```

그리고 생성한 DOM 엘리먼트의 자식 노드를 생성합니다.

현재 자식노드는 문자열 하나뿐이므로, 텍스트 노드 하나만 생성하면 됩니다.

```javascript
const text = document.createTextNode("");
test["nodeValue"] = element.props.children;
```

이제 생성한 문자열을 DOM element에 추가하고, 이 DOM element를 container에 추가합니다.

```javascript
node.appendChild(text);
container.appendChild(node);
```

끝입니다.

리액트를 사용하지않고, 리액트를 사용한 것과 동일한 결과를 만들어내는 코드를 완성했습니다.

```javascript
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello",
  },
};

const container = document.getElementById("root");

const node = document.createElement(element.type);
node["title"] = element.props.title;

const text = document.createTextNode("");
text["nodeValue"] = element.props.children;

node.appendChild(text);
container.appendChild(node);
```



## Step I: createElement 함수

아래와 같은 리액트 코드를 교체해봅시다.

```jsx
const element = (
	<div id="foo">
  	<a>bar</a>
    <b />
  </div>
);

const container = document.getElementById("root");
ReactDOM.render(element, container);
```

우선 바벨을 돌렸다고 생각하며, JSX를 `React.createElement` 함수를 사용하도록 바꿔봅니다.

```javascript
const element = React.createElement(
	"div",
  {
    id: "foo"
  },
  React.createElement("a", null, "bar"),
  React.createElemeent("b")
)
```

`React.createElement` 함수를 들여다봅시다. `type`, `props` 인자를 받아 간단한 객체를 리턴하는 함수일 뿐입니다.

```javascript
const createElement(type, props, ...children) => {
  return {
    type,
    props: {
      ...props,
      children,
    }
  }
}
```

`props` 에는 spread operator를 사용 하였습니다.

`children` 에는 rest parameter를 사용하였습니다.

예를 들어,

 `createElement("h1")` 은 다음을 리턴합니다.

```javascript
{
	"type": "h1",
  "props": {
    "children": []
  } 
}
```

`createElement("div", {name: 'junwoo', age:100}, 'apple')` 은 다음을 리턴합니다.

```javascript
{
    "type": "div",
    "props": {
      "name": "junwoo",
      "age": 100,
      "children": ["apple"]
    },

}
```

`createElement("li", null, 'apple', 'banana', 'pears')` 는 다음을 리턴합니다.

```javascript
{
  "type": "li",
  "props": {
    "children": ["apple", "banana", "pears"]
  }
}
```



