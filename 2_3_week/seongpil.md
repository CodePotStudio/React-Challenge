# 성능 최적화

상태 업데이트가 느리다면?

1. production 빌드를 실행 중인지 확인
2. 상태를 트리에서 불필요하게 높은 곳에 두지 않는다.
    - ex) input의 상태를 중앙화된 스토어나 트리 최상단에서 관리
3. React DevTools Profiler를 통해 리렌더링 되는 곳을 확인하여 `memo()` 혹은 `useMemo()`로 감싼다.

이외에 어떤 테크닉이 있을까?

## (인위적으로) 느린 컴포넌트

```javascript
export default function App() {
  let [color, setColor] = useState('red');
  return (
    <>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
      <ExpensiveTree />
    </>
  );
}

function ExpensiveTree() {
  let now = performance.now();
  while (performance.now() - now < 100) {
    // 인위적인 지연 -- 100ms 동안 아무것도 하지 않음
  }
  return <p>I am a very slow component tree.</p>;
}
```
[Demo](https://codesandbox.io/s/frosty-glade-m33km?file=/src/App.js:23-513)

매우 버벅임을 알 수 있다. 

```javascript
let ExpensiveTree = React.memo(() => {
  let now = performance.now();
  while (performance.now() - now < 100) {
    // Artificial delay -- do nothing for 100ms
  }
  return <p>I am a very slow component tree.</p>;
});
```

이렇게 `memo`를 사용해 끝낼 수도 있지만, 다른방법도 있다!

### Solution 1. 상태를 아래로 내리기

자세히 보면 실제로 반환 된 트리의 일부만 color와 관련이 있음을 알 수 있다.

```javascript
let [color, setColor] = useState('red');
return (
    <div>
        /* [여기서부터] */
        <input value={color} onChange={(e) => setColor(e.target.value)} />
        <p style={{ color }}>Hello, world!</p>
        /* [여기까지] */
        <ExpensiveTree />
    </div>
);
```

그렇다면 아예 이를 분리하면 된다.

```javascript
export default function App() {
  return (
    <>
      <Form />
      <ExpensiveTree />
    </>
  );
}

function Form() {
  let [color, setColor] = useState('red');
  return (
    <>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
    </>
  );
}
```

color가 바뀌어도 `Form` 컴포넌트만 변경이 일어날 뿐 App아래의 `ExpensiveTree`의 렌더링에는 영향을 주지 못한다.

### Solution 2. 내용물을 끌어올리기

하지만 첫번째 해결방법은 상태가 트리 위에서 사용되는 경우에는 소용이 없다.

예를 들어 부모 <div>에 color를 넘긴다고 가정해 보면

```javascript
export default function App() {
  let [color, setColor] = useState('red');
  return (
    <div style={{ color }}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}
```

다시 `memo`를 쓸 수밖에 없을 것 같지만 의외로 간단히 해결할 수 있다.

```javascript

---

__reference__

https://overreacted.io/ko/before-you-memo/