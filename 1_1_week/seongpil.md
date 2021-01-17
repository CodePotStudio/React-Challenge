# React Hook mental model

클래스 컴포넌트의 라이프 사이클 훅은 이름이 직관적입니다. 필요한 상황에 맞게 사용하기 쉽죠.
하지만 v16.8부터 Hook이 전면으로 나오고 기존의 라이프 사이클에 맞추어 코드를 짜면 무언가 어색함이 느껴집니다. 무엇보다 이름이 전혀 와닿지 않습니다! `useState, useEffect, useCallback` 등등... 리액트 팀은 왜 이런 __이름을__ 지었을까요?

리액트 진영의 네임드인 Dan Abramov마저 이 께름칙함을 고백하며 몇가지 케이스를 소개합니다.
- `useEffect`로 `componentDidMount` 동작을 흉내내려면 어떻게 하지?
- `useEffect` 안에서 데이터 페칭(Data fetching)은 어떻게 해야할까? 두번째 인자로 오는 배열(`[]`) 은 뭐지?
- 이펙트를 일으키는 의존성 배열에 함수를 명시해도 되는걸까?
- 왜 가끔씩 데이터 페칭이 무한루프에 빠지는걸까?
- 왜 가끔씩 이펙트 안에서 이전 state나 prop 값을 참조할까?

결과적으로 보면 이는 리액트를 보는 새로운 관점이 필요함을 시사합니다.
그리고 Dan은 이에관해 우리에게 한가지 사실을 일러줍니다.

> 이전에 배운건 잊어버리세요.

## Each Render Has Its Own Props and State

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </>
  );
}
```
이 컴포넌트가 어떻게 화면에 그려질지 예측이 되시나요? 여기서 `count`는 시간이 지남에 따라 값이 변하는 존재가 아닙니다. `setCount`로 다시 __렌더링 될 때 마다__ 새로운 함수가 생겨나고 `count`는 그 안에 상수로써 존재합니다.

```javascript
// 1. 처음 랜더링 시
function Counter() {
  const count = 0; // useState() 로부터 리턴
  // ...
  <p>You clicked {count} times</p>
  // ...
}

// 2. 버튼을 클릭하면 함수가 다시 호출된다
function Counter() {
  const count = 1; // useState() 로부터 리턴
  // ...
  <p>You clicked {count} times</p>
  // ...
}

// 3. 또 한번 클릭하면, 다시 함수가 호출된다
function Counter() {
  const count = 2; // useState() 로부터 리턴
  // ...
  <p>You clicked {count} times</p>
  // ...
}
```
우리 눈에 보이는 `Counter`컴포넌트는, 매번 __새로운 함수의 결과를__ 화면에 반영한 것 입니다. 여기에는 `watcher`, `proxy`, `data binding` 같은 어떤 특별한 magic도 __없습니다.__

각 3개의 함수마다 고유의 `count`값을 갖고 있고, React가 이를 DOM에 반영할 뿐입니다.

## Each Render Has Its Own Event Handlers

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  function handleAlertClick() {
    setTimeout(() => {
      alert('You clicked on: ' + count);
    }, 3000);
  }
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>

      {/* NEW */}
      <button onClick={handleAlertClick}>
        Show alert
      </button>
    </div>
  );
}
```
1. 카운터를 3 증가시킨다
2. "Show alert"를 누른다
3. 타임아웃이 실행되기 전에 카운터를 5로 증가시킨다.

![](https://overreacted.io/46c55d5f1f749462b7a173f1e748e41e/counter.gif)

~~답: 3~~

> 클래스 컴포넌트였다면 5가 나올수도 있습니다. (물론 3이 나오게할 방법이 있기 때문에 가능성으로 표현)

매 랜더링 시, 그 내부 props와 state는 영원히 같은 상태로 유지됩니다.

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setTimeout(() => {
      console.log(`You clicked ${count} times`);
    }, 3000);
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

![](https://overreacted.io/a5727d333c270e05942f508707265378/timeout_counter.gif)

## Each Render Has Its Own Effects

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

`useEffect`로 감싸진 함수도 이전 예제들과 마찬가지로, 매 렌더링마다 새로 호출되는 함수입니다. 다만 순서가 있습니다. return된 JSX가 DOM에 반영된 뒤(화면이 다 그려진 뒤) 호출됩니다.

그리고 그 내부적으로 사용하는 `count`또한 렌더링 당시의 `count`와 묶여 있습니다.

사실 지금까지 함수형 컴포넌트의 원리는 클로져와 완전히 동일합니다.

```javascript
for (var i = 0; i < 4; i++) {
    setTimeout(() => console.log(i), 1000);
}
// 예상해보기

for (var i = 0; i < 4; i++) {
    ((i) => setTimeout(() => console.log(i), 1000))(i);
}
```

함수형 컴포넌트에서 props와 state는 불변값이지만, 클래스 컴포넌트의 props와 state는 `this`로 인해 가변이됩니다.(구조분해 할당, 혹은 변수 할당을 통해 불변으로 만들 수 있지만 매번 변수를 할당하는 불편함이 생깁니다)

## 흐름을 거슬러 올라가기

앞서 state와 props는 매 렌더링 안에서 불변이라는 것을 확인했습니다.
따라서 아래 코드는 완전히 동일합니다.
```javascript
function Example(props) {
  useEffect(() => {
    setTimeout(() => {
      console.log(props.counter);
    }, 1000);
  });
  // ...
}
function Example(props) {
  const counter = props.counter;
  useEffect(() => {
    setTimeout(() => {
      console.log(counter);
    }, 1000);
  });
  // ...
}
```

만약 최신의 `counter`, 즉 렌더링 시점의 값과 상관 없이 최신의 값을 사용하고 싶을 땐 어떻게 할까요 

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  const latestCount = useRef(count);
  useEffect(() => {
    // 변경 가능한 값을 최신으로 설정한다
    latestCount.current = count;
    setTimeout(() => {
      // 변경 가능한 최신의 값을 읽어 들인다
      console.log(`You clicked ${latestCount.current} times`);
    }, 3000);
  });
  // ...
}
```

![](https://overreacted.io/78f7948263dd13b023498b23cb99f4fc/timeout_counter_refs.gif)

class 컴포넌트는 이런식으로 `this.state`를 재할당하고 있습니다.

## 그러면 클린업(cleanup)은 뭐지?

리액트 공식 문서에 있는 예제입니다.
```javascript
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
  };
});
```
첫번째 렌더링에서 `props`이 `{id:1}`, 두번째에서 `{id:2}`라고 가정합시다.
다음은 우리의 예상입니다.
- 리액트가 `{id:1}`을 다루는 이펙트를 클린업한다.
- 리액트가 `{id:2}`로 UI를 렌더링한다.
- 리액트가 `{id:2}`로 이펙트를 실행한다.

이는 class 컴포넌트의 라이프사이클 멘탈 모델을 적용한 흐름입니다. 그러므로 틀렸습니다.

[공식 문서](https://ko.reactjs.org/docs/hooks-reference.html#timing-of-effects)에서 볼 수 있듯 useEffect는 모든 렌더링을 완료한 후 실행됩니다.(이펙트가 스크린 업데이트를 가로막지 않기 위함 자세한 내용은 [영상](https://www.youtube.com/watch?v=ZCuYPiUIONs&t=431s)으로 확인)

- 리액트가 `{id:2}`로 UI를 렌더링한다.
- 브라우저가 실제로 화면에 `{id:2}`를 반영한 UI를 그린다.
- 리액트가 `{id:1}`을 다루는 클린업을 실행한다.
- 리액트가 `{id:2}`로 이펙트를 실행한다.

앞서서 여러번 설명했듯 매 렌더링마다 내부의 __모든 함수는__ 불변의 props/state를 바라보기 떄문에 `{id:1}`의 클린업이 중간에 있어도 이상하지 않습니다.

## 라이프사이클이 아니라 동기화

useEffect는 __리액트 트리 바깥에__ 있는 것들을 __props와 state에 따라 동기화__ 할 수 있게 합니다.

리액트는 우리가 지정한 props와 state에 따라 DOM과 동기화합니다. 랜더링 시 "마운트" 와 "업데이트" 의 구분이 없습니다.

```javascript
function Greeting({ name }) {
  useEffect(() => {
    document.title = 'Hello, ' + name;
  });
  return (
    <h1 className="Greeting">
      Hello, {name}
    </h1>
  );
}
```
_이제 위 코드에서 `마운트/업데이트/언마운트`는 잊어야합니다._

컴포넌트가 첫번째로 랜더링할 때와 그 후에 다르게 동작하는 이펙트를 작성하려고 한다면, 위에서 언급한 것처럼 흐름을 거슬러야합니다. 

DOM의 동기화는 지금껏 아무런 의심없이 이루어졌습니다. 그렇다면 Effect는 어떨까요.
Effect를 버그 없이(글 초반에 제시한 문제들을) 수행할 수 있을까요?

## 리액트에게 이펙트를 비교하는 법을 가르치기

리액트는 바뀐 DOM만 업데이트 합니다.

```html
<!-- 아래 DOM을 -->
<h1 className="Greeting">Hello, Dan</h1>

<!-- 이렇게 업데이트 한다면 -->
<h1 className="Greeting">Hello, Yuzhi</h1>
```

리액트는 두 객체를 비교합니다.
```javascript
const oldProps = {className= 'Greeting', children: 'Hello, Dan'};
const newProps = {className= 'Greeting', children: 'Hello, Yuzhi'};
```

props를 비교했을 때 className는 동이하고 children만 다릅니다. 그래서 다음 코드만 호출됩니다.

```javascript
domNode.innerText = 'Hello, Yuzhi';
// domNode.className은 건드리지 않음
```

이처럼 이펙트도 비교하여 호출을 최적화 할 수 있을까요? 아래 컴포넌트는 매 클릭마다 이펙트를 다시 실행할겁니다.

```javascript
function Greeting({ name }) {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    document.title = 'Hello, ' + name;
  });

  return (
    <h1 className="Greeting">
      Hello, {name}
      <button onClick={() => setCounter(count + 1)}>
        Increment
      </button>
    </h1>
  );
}
```

하지만 이는 불합리합니다. name은 변화가 없고 단지 count만 변하니까요. 하지만 이펙트는 함수를 받고있고, 이 함수는 실행되기 전까지 다른 결과를 불러오는 함수인지 아닌지 리액트는 알 수 없습니다.

```javascript
let oldEffect = () => { document.title = 'Hello, Dan'; };
let newEffect = () => { document.title = 'Hello, Dan'; };
```

여기서 우리가 알고 있는 `deps`가 등장합니다. 리액트에게 의존성 배열을 알려줍니다.

```javascript
useEffect(() => {
  document.title = 'Hello, ' + name;
}, [name]); // 우리의 의존성
```

> Hey react! 네가 이 함수의 안쪽을 볼 순 없지만, 그 안에서 `name`이외의 값은 쓰지 않는다고 약속할게! 

```javascript
const oldEffect = () => { document.title = 'Hello, Dan'; };
const oldDeps = ['Dan'];
const newEffect = () => { document.title = 'Hello, Dan'; };
const newDeps = ['Dan'];
```

이전과 현재의 의존성에 변화가 없으므로 리액트는 이펙트를 실행하지 않습니다. 의존성 배열안에 하나라도 값이 다르다면 이펙트는 실행될겁니다. 리액트는 모든걸 __동기화 해야하니까요.__

## 리액트에게 의존성으로 거짓말하지 마라

함수형 컴포넌트에서 클래스형 컴포넌트의 습관을 이어갈 때 아래와같은 실수를 저지르기 쉽습니다.(최초 렌더링시 데이터 fetching)

```javascript
function SearchResults() {
  async function fetchData() { /* ... */ }

  useEffect(() => {
    fetchData();
  }, [])}
```

> 그러면 처음에만 데이터를 불러오고 싶은데 어떻게 합니까?

위 문제는 차차 해결하고 아래와 같은 사항을 기억합시다.
- `deps`를 지정한다면, 컴포넌트에 있는 모든 값(state, props, 함수) 중 그 이펙트에 사용될 값은 반드시 거기(deps)에 있어야 한다
- 의존성을 제거하는 것(빈 배열)은 문제를 해결하는 능사는 아니다

## 의존성으로 거짓말을 하면 생기는 일

아래 코드는 잘 작동합니다.
```javascript
useEffect(() => {
  document.title = 'Hello, ' + name;
}, [name]);
```
하지만 아래 코드는 제대로 동작하지 않습니다.
```javascript
useEffect(() => {
  document.title = 'Hello, ' + name;
}, []);
```
여기까진 꽤 명확해 보입니다. 조금 더 복잡한 예제를 살펴보죠.

매 초마다 숫자가 올라가는 카운터를 작성해봅시다. 클래스 컴포넌트 기반의 개념을 적용한다면
- 인터벌을 한번만 설정하고
- 한번만 제거하자

```javascript
class Counter extends React.Component {
  state = { count: 0 };

  componentDidMount() {
    this.interval = setInterval(this.tick, 1000);
  }
  componentWillUnmount() {
    clearInterval(this.interval);
  }
  tick = () => {
    this.setState({
      count: this.state.count + 1
    });
  }
  render() {
    return <h1>{this.state.count}</h1>;
  }
}
```
잘 동작하는 코드입니다. 이를 함수형으로 옮겨보겠습니다.

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []); // 흠...
  return <h1>{count}</h1>;
}
```
이 코드는 잘 동작하지 않습니다. [숫자는 한번만 증가합니다.](https://codesandbox.io/s/91n5z8jo7r)

여러분의 멘탈 모델이 기존의 "의존성 배열은 내가 언제 이펙트를 다시 실행해야 할지 지정할 때 쓰인다."라면 납득되지 않는 결과입니다. 왜그럴까요?

앞서 당부드렸던 것이 기억나시나요? 의존성 배열은 리액트에게 렌더링 스코프(컴포넌트 안쪽, 이펙트 바깥)에 나온 값 중 이펙트에 쓰이는 것을 전부 알려주는 것이라고요.

분명 이펙트 안에서 count를 사용했지만 의존성 배열에는 아무것도 없습니다. __여러분의 거짓말로 버그가 터졌습니다!__

일단 차분히 디버깅을 해봅시다. 첫번쨰 렌더링에서 count는 몇인가요? 네, 0입니다. 그렇다면 처음 인터벌 안에서 실행된 코드는 다음과 같습니다 

```javascript
setCount(0 + 1); // 매 초마다 실행되는 함수는 이것입니다.
```

setCount를 호출 했으니 리액트는 두번째 렌더링을 실행합니다. 하지만 의존성배열은 비어있으므로 이전과 동일하기 때문에 우리의 이펙트는 절대 실행되지 않습니다.

기억 나시나요? 함수형 컴포넌트는 매 렌더링 마다 고유의/불변의 props,state, 함수를 갖습니다. `setInterval`로 `setCount(count + 1)`을 매 초마다 실행하지만 이 때 우리가 기대한건 `count`가 0, 1, 2, 3...으로 증가하는 것이지만, 저 count는 영원히 __0__ 입니다. 그래서 화면엔 1만 보이는 것이죠. `setInterval`안에서 `console.log(count)`를 실행해 보시면 더 확실히 알 수 있습니다.

이제 리액트에게 거짓말을 하면 어떤 일이 생기는지 확인했습니다. 이제부터는 솔직하게 의존성을 명시한느 것을 대원칙으로 받아드립시다.

## 의존성을 솔직하게 적는 2가지 방법

> 첫번째 방법을 사용해보고, 필요하다면 두번째 방법을 사용하세요.

### 1. 컴포넌트 안에 있고, 이펙트에서 사용되는 모든 값을 deps에 추가

```javascript
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, [count]);
```
네, 동작은 하지만 우리가 원하는 형태는 절대 아닙니다.(setInterval이 매 렌더링마다 실행되고, 클린업 될테니까요) 하지만 우리가 고쳐야할 첫번째 문제를 해결했습니다.

### 2. 이펙트 코드를 수정하여, 우리가 원하는 것보다 자주 바뀌는 값을 요구하지 않도록 만들기

말이 어려운데, 의존성에 대해 거짓말을 하지 않은 채 의존성을 적게 넘겨주라는 뜻입니다.

## 이펙트가 자급자족 하도록 만들기

이펙트에서 __왜 `count`를 쓰고 있나요?__ 오직 `setCount`를 위해 사용하고 있습니다. 이런 경우 간단히 해결할 수 있습니다. `setState`의 [함수형 업데이터](https://reactjs.org/docs/hooks-reference.html#functional-updates)를 사용하면 됩니다.

```javascript
useEffect(() => {
  const id = setInterval(() => {
    setCount(c => c + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

```
count + 1
```
이 코드는 `count`에 1을 더해서 리액트에게 __돌려주기 위해__ 사용됐습니다. 리액트는 사실 `count`를 이미 알고 있습니다. 우리가 진짜로 리액트에게 알려줘야하는 건 __"지금 값이 뭐든 간에 1을 더해줘"__ 입니다.

---
https://rinae.dev/posts/a-complete-guide-to-useeffect-ko