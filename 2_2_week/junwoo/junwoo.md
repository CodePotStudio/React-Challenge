# 2주차: [Build your own React](https://pomb.us/build-your-own-react/) 을 학습하며

## Step 0: Basic Review

간단한 리액트 코드가 있습니다.

```jsx
const element = <h1 title="foo">Hello</h1>; // 리액트 엘리먼트를 정의한다.
const container = document.getElementById("root"); // DOM으로부터 노드를 얻는다.
ReactDOM.render(element, container); // 컨테이너 안에 리액트 엘리먼트를 생성한다.
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
    title: "foo",
  },
  "Hello"
);
```

`React.createElement` 함수는 전달받은 아규먼트들로 객체를 생성하게됩니다.

`React.createElement` 함수를 실행한 결과는 다음과 같습니다.

```javascript
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello",
  },
};
```

`type` 과 `props` 를 객체 키 값으로 가지는 객체가 생성됩니다. (실제로는 더 많은 속성이 있다고 하네요.)

현재 결과물은 다음과 같습니다.

```jsx
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello",
  },
};
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

세번째 줄의 `ReactDOM.render(element, container);` 코드 또한 바닐라 자바스크립트로 바꿔봅니다.

우선 DOM 엘리먼트를 생성합니다.

```javascript
const node = document.createElement(element.type); // "h1"
node["title"] = element.props.title; // "foo"
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

Step 의 목적은, 나만의 리액트 라이브러리를 만드는겁니다.

시작해봅니다.

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
    id: "foo",
  },
  React.createElement("a", null, "bar"),
  React.createElemeent("b")
);
```

`React.createElement` 함수를 들여다봅시다. `type`, `props` 인자를 받아 간단한 객체를 리턴하는 함수일 뿐입니다.

```javascript
const createElement = (type, props, ...children) => {
  return {
    type,
    props: {
      ...props,
      children,
    },
  };
};
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

또한, `children` 배열의 값이 string이나 number타입과 같은 기본 타입이라면 텍스트 노드로 변경하는 처리를 해주도록 합니다.

```javascript
const createTextElement = (text) => {
  return {
    type: "TEXT_ELEMENT",
    props: {
      nodeValue: text,
      children: [],
    },
  };
};

const createElement = (type, props, ...children) => {
  return {
    type,
    props: {
      ...props,
      children: children.map((child) => {
        return typeof child === "object" ? child : createTextElement(child);
      }),
    },
  };
};
```

다시 코드로 돌아와보면..

```javascript
const element = React.createElement(
  "div",
  {
    id: "foo",
  },
  React.createElement("a", null, "bar"),
  React.createElemeent("b")
);

const container = document.getElementById("root");
ReactDOM.render(element, container);
```

아직까지는 `React.createElement` 를 사용하고있는 상태입니다.

리액트의 `createElement` 대신, 우리가 새로 만드는 라이브러리를 사용하도록 교체해봅니다.

라이브러리 이름은 `Junwoo` 로 결정..

```javascript
...

const Junwoo = {
  createElement
};

const element = Junwoo.createElemenet(
	"div",
  {
    id: "foo"
  },
  Junwoo.createElement("a", null, "bar"),
  Junwoo.createElemeent("b")
);
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

여기서 하나만 더 해봅니다.

`Junwoo.createElement` 를 직접 사용하지 않고, JSX 문법을 그대로 사용하고 싶은 경우에, 바벨에게 리액트의 대신 `Junwoo.createElement` 를 사용하도록 알려줄 수 있습니다.

```jsx
...

const Junwoo = {
  createElement
};

/** @jsx Didact.createElement */
const element = (
	<div id="foo">
    <a>bar</a>
    <b />
  </div>
)
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

코멘트를 위와같이 추가하면, 바벨이 JSX를 자바스크립트로 트랜스파일할 때, 우리가 정의한 함수를 사용하도록 지정해줄 수 있습니다.

## Step II: render 함수

Step I 의 코드 중, 일부를 가져와보겠습니다.

```jsx
const Junwoo = {
  createElement,
};

/** @jsx Didact.createElement */
const element = (
  <div id="foo">
    <a>bar</a>
    <b />
  </div>
);
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

이제 `ReactDOM.render` 함수를 새로 만들어봅시다.

```jsx
const render = (element, container) => {
  // TODO
};

const Junwoo = {
  createElement,
  render,
};

/** @jsx Didact.createElement */
const element = (
  <div id="foo">
    <a>bar</a>
    <b />
  </div>
);
const container = document.getElementById("root");
Junwoo.render(element, container);
```

`render` 함수는 전달받은 element 인자의 `type` 을 사용하여 DOM 노드를 생성하는 것부터 시작합니다.

그리고 만들어진 노드를 컨테이너에 추가합니다.

```javascript
const render = (element, container) => {
  const dom = document.createElement(element.type);

  container.appendChild(dom);
};
```

이 과정을 각각 자식들 모두에게 재귀적으로 수행하도록 코드를 수정합니다.

```javascript
const render = (element, container) => {
  const dom = document.createElement(element.type);

  element.props.children.forEach((child) => render(child, dom));

  container.appendChild(dom);
};
```

또한 텍스트 엘리먼트도 처리해야합니다.

만약 타입이 `TEXT_ELEMENT` 인 경우에는 일반적인 DOM 노드 대신 텍스트 노드를 생성하도록 코드를 수정해봅니다.

```javascript
const render = (element, container) => {
  // const dom = document.createElement(element.type);
  const dom =
    element.type === "TEXT_ELEMENT"
      ? document.createTextNode("")
      : document.createElement(element.type);

  element.props.children.forEach((child) => render(child, dom));

  container.appendChild(dom);
};
```

마지막으로, DOM 노드에게 전달받은 속성들을 부여해야합니다.

```javascript
const render = (element, container) => {
  const dom =
    element.type === "TEXT_ELEMENT"
      ? document.createTextNode("")
      : document.createElement(element.type);

  const isProperty = (key) => key !== "children";
  Object.keys(element.props)
    .filter(isProperty)
    .forEach((name) => {
      dom[name] = element.props[name];
    });

  element.props.children.forEach((child) => render(child, dom));

  container.appendChild(dom);
};
```

끝입니다.

이제 JSX를 DOM으로 렌더링할 수 있는 라이브러리를 만들었습니다.

```jsx
const createTextElement = (text) => {
  return {
    type: "TEXT_ELEMENT",
    props: {
      nodeValue: text,
      children: [],
    },
  };
};

const createElement = (type, props, ...children) => {
  return {
    type,
    props: {
      ...props,
      children: children.map((child) => {
        return typeof child === "object" ? child : createTextElement(child);
      }),
    },
  };
};

const render = (element, container) => {
  const dom =
    element.type === "TEXT_ELEMENT"
      ? document.createTextNode("")
      : document.createElement(element.type);

  const isProperty = (key) => key !== "children";
  Object.keys(element.props)
    .filter(isProperty)
    .forEach((name) => {
      dom[name] = element.props[name];
    });

  element.props.children.forEach((child) => render(child, dom));

  container.appendChild(dom);
};

const Junwoo = {
  createElement,
  render,
};

/** @jsx Didact.createElement */
const element = (
  <div id="foo">
    <a>bar</a>
    <b />
  </div>
);
const container = document.getElementById("root");
Junwoo.render(element, container);
```

## Step III: 동시성 모드(Concurrent Mode)

리팩토링을 할 시간입니다.

```javascript
const render = (element, container) => {
  ...

  element.props.children.forEach(child => render(child, dom));

  ...
};
```

`render` 함수를 호출하여 렌더링을 시작하면, 모든 엘리먼트 트리를 렌더링하는 것을 끝마치기 전까지는 멈출 수 없습니다.

만약 엘리먼트 트리가 너무나 너무나 크다면, 메인 스레드의 동작이 너무 오랫동안 멈춰있을 것입니다.

따라서 작업을 더 작은 단위로 나눈 다음, 각각의 단위마다 브라우저가 어떤 작업이 필요한 경우 렌더링 도중에 끼어들 수 있도록 리팩토링할 것입니다.

> 음.. 이 다음부터는 동시성을 지원하는 코드인데 아직 제가 이해하기에 많이 벅차네요 =\_=,,;
>
> 리액트를 더 사용해보고 이해한 후 다시 봐야겠네요;



---

## [The one thing that no one properly explains about React — Why Virtual DOM](https://hashnode.com/post/the-one-thing-that-no-one-properly-explains-about-react-why-virtual-dom-cisczhfj41bmssp53mvfwmgrq) 을 읽고 학습



### 브라우저 렌더링 과정

![pic1](./pic1.png)



#### DOM 트리 생성

브라우저 HTML을 전달받으면, 이를 파싱하고 DOM 노드로 이루어진 트리를 만든다.

#### 렌더 트리 생성

CSS 파일과 각 엘리먼트의 인라인 스타일을 파싱한다.

파싱한 결과를 사용하여 DOM 트리를 따라 새로운 트리, 렌더 트리를 만든다.

즉 각 요소의 스타일이 계산되고, 계산되는 과정에 다른 요소들의 스타일 속성을 참조한다.

#### 레이아웃(리플로우)

렌더 트리가 다 만들어지고 나면, 레이아웃 과정을 거친다.

각 노드들은 스크린 상의 좌표가 주어져서, 정확히 어디에 위치해야하는지 결정된다.

#### 페인팅

렌더링된 요소들에 색을 입히는 과정이다.

트리의 각 노드들을 거쳐가며 paint() 메소드를 호출한다.

그러면 스크린에는 원하는 정보가 나타난다.



### 가상 DOM

DOM에 변화가 생기면? -> 렌더트리를 재생성한다. -> 즉, 모든 요소들의 스타일이 다시 계산된다. -> 레이아웃을 만든다. -> 페인팅을 한다.

이 부분에서 가상 DOM이 빛을 발하는데, 뷰에 변화가 생기면 실제 DOM에 적용되기 전에, 가상 DOM에 먼저 적용을 시키게된다.

그리고 그 최종적인 결과를 실제 DOM으로 전달해주게되어서, 브라우저 내에서 발생하는 연산의 양을 줄이게 된다.

>DOM 조작의 문제는, 각 조작들이 레이아웃의 변화, 트리의 변화, 렌더링의 발생을 일으킨다는 것이다.
>
>예를 들어 30개의 노드를 하나하나 수정한다면, 이 말은 즉 30번의 레이아웃 재계산과 30번의 리렌더링을 뜻하게 된다.
>
>가상 DOM은 단순히 모든 변화를 하나로 묶어서 실제 DOM에게 던져준다. 
>
>레이아웃 계산과 리렌더링의 규모는 커지겠지만, 연산의 횟수를 줄이게되는 것이다.