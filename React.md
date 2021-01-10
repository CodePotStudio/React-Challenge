# React
## Hooks
- React state와 lifecycle을 연동할 수 있게 해주는 함수
- class 안에서는 동작하지 않음
### 사용 규칙
- 최상위에서만 Hook을 호출해야함.(반복, 조건, 중첩 함수 내에서 실행 X)
- `Functional Component 내에서만 호출`(+ Custom Hook) => 일반 js함수 내에서는 호출하면 안됨
### useState
- 현재 state의 초기 값, 이 state를 변경하는 함수 제공
```typescript
const [value, setValue] = useState(0);

<button onClick={()=>setValue(value+1)}>Button</Button>
```
### Effect Hooks
- React Component 안에서 데이터를 가져오거나 subscribe, DOM을 직접 조작하는 작업을 side effect라 함(다른 Component에 영향을 줄 수도 있고, 렌더링 과정에서는 구현할 수 없는 작업이기 때문)
- Effect Hook들은 side effects를 수행할 수 있게 해줌
- class형 Component의 componentDidMoun, componentDidUpdate, componentWillUnmount같은 목적으로 제공
#### useEffect
- Component가 Render된 후 어떤 일을 수행해야 하는 지
- Component 내의 어떤 prop에도 접근할 수 있게 하기 위해 함수 내에 작성
- 사용시 React가 DOM을 바꾼 후에 effect 함수를 실행
- 매 렌더링 이후 effect를 실행
- 해제를 위해서는 해제 함수를 반환(Optional)
```typescript
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => { //Component가 Unmount될 때 ChatAPI에서 구독을 해지, 재 렌더링이 일어나 effect를 재실행하기 전에도 해지
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```
- 조건부 effect
```typescript
useEffect(()=>{
    document.title = `You Clicked ${count} times`;
}, [count]); // count가 바뀔 때에만 effect를 재실행

useEffect(()=>{
    document.title = "You Clicked";
}, []); //최초 1회만 effect 실행
```

### useCallback --- todo: callback 함수 알아보기
- Memoization된 Callback 반환
- inline callback과 의존성 값의 배열을 전달
```typescript
const callbackFunction = useCallback(()=>{
    doSomething(a,b);
}, [a,b]);
```

### useMemo
- memoization된 값을 반환
- create 함수와 의존성 값 배열 전달시 의존성이 변경되었을 때에 memoization 값 다시 계산
```typescript
const memoizedValue = useMemo(() => computeValue(a,b), [a,b]);
```
### useHistory
jandi tests