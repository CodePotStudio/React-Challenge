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

> 클래스 컴포넌트였다면 5가 나올수도 있다. (물론 3이 나오게할 방법이 있기 때문에 가능성으로 표현)

매 랜더링 시, 그 내부 props와 state는 영원히 같은 상태로 유지됩니다.

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

## 흐름을 거슬러 올라가기


---
https://rinae.dev/posts/a-complete-guide-to-useeffect-ko