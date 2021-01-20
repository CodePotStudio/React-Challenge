# React Hook mental model

## 액션을 업데이트로부터 분리하기

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + step);
    }, 1000);
    return () => clearInterval(id);
  }, [step]);
  return (
    <>
      <h1>{count}</h1>
      <input value={step} onChange={e => setStep(Number(e.target.value))} />
    </>
  );
}
```
[데모](https://codesandbox.io/s/zxn70rnkx)

위 코드는 잘 동작합니다. 다만 마음에 걸리는 것은 step이 바뀔때 마다 `interval`이 clear되고 다시 시작되는 것이죠. 이러한 초기화를 하지 않으려면 어떻게 해야할까요? 그러기 위해 deps에서 `step`을 지워야할텐데 어떻게 해야할까요?

__어떤 상태 변수(count)가 다른 상태 변수의 현재 값(step)에 연관되도록 설정하려고 한다면, 두 상태 변수 모두 useReducer 로 교체해야 할 수 있습니다.__

혹시 여러분의 코드에서 `setSomething(something => ...)` 코드가 보인다면, 리듀서를 고려해보기 좋은 타이밍입니다.

리듀서는 __컴포넌트에서 일어나는 "액션"의 표현(여기서는 setCount(...)겠죠?)과 그 반응으로 상태가 어떻게 업데이트되어야 할지를(c => c + step) 분리합니다.__

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
const { count, step } = state;

useEffect(() => {
  const id = setInterval(() => {
    dispatch({ type: 'tick' }); // setCount(c => c + step) 대신에
  }, 1000);
  return () => clearInterval(id);
}, [dispatch]);
```
`dispatch`는 컴포넌트가 사라지지 않는 한 항상 같습니다. 따라서 __interval은 초기화되지 않습니다.__(문제 해결!)

이처럼 이펙트 안에서 상태를 읽는 대신 _무슨일이 일어날지_ 알려주는 정보를 인코딩하는 _액션_을 전파합니다.

이로써 이펙트는 __어떻게__ 상태를 업데이트할지 신경쓰지 않고, 단지 __무슨일이 일어날지__ 알려줍니다. 그 끝에서 리듀서는 업데이트 로직을 담아냅니다.

```javascript
const initialState = {
  count: 0,
  step: 1,
};

function reducer(state, action) {
  const { count, step } = state;
  if (action.type === 'tick') {
    return { count: count + step, step };
  } else if (action.type === 'step') {
    return { count, step: action.step };
  } else {
    throw new Error();
  }
}
```

## 왜 `useReducer`가 `Hooks` 치트 모드인가

지금까지 이펙트가 이전 상태를 기준으로 상태를 변경할 때, 의존성을 제거하는지 살펴 봤습니다. 하지만 __다음 상태를 계산하는데 props가 필요하다면?__ 다음과 같이 컴포넌트를 작성한다면 어떨까요?

```javascript
<Counter step={1} />
```

리듀서는 `props`를 알지 못하기 때문에 deps에 넣어야 합니다.

```javascript
// 하지만 reducer를 컴포넌트 안에 작성한다면?
function Counter({ step }) {
  const [count, dispatch] = useReducer(reducer, 0);

  function reducer(state, action) {
    if (action.type === 'tick') {
      return state + step;
    } else {
      throw new Error();
    }
  }

  useEffect(() => {
    const id = setInterval(() => {
      dispatch({ type: 'tick' });
    }, 1000);
    return () => clearInterval(id);
  }, [dispatch]);

  return <h1>{count}</h1>;
}
```

- `dispatch`는 액션(`{type: 'tick'}`)만을 기억한 채 rerendering을 실행합니다.
- 다음 렌더링시에 컴포넌트 내부의 reducer가 다시 useReducer에 의해 호출됩니다.
- 이때 props는 reducer함수 스코프에서 읽을 수 있으므로 이펙트 내부와는 관련이 없게됩니다.

이로써 __업데이트 로직과 그로 인해 무엇이 일어나는지 서술하는 것을 분리할__ 수 있도록 만들어줍니다

---

#### Reference
https://overreacted.io/ko/a-complete-guide-to-useeffect/