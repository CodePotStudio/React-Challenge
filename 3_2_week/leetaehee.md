# 1. state에 저장한 배열 렌더링하기
React에서는 `state` 내부 값을 직접적으로 수정해서는 안되기 때문에 `state`에 저장된 배열에 변화를 줄 때에는 불변성을 지켜주어야 합니다. 불변성을 지키기 위해 기존 배열을 변화시키지 않고 기존 배열에 기반한 새로운 배열을 만든 후 `setState`를 통해 `state`에 저장된 배열을 수정합니다. 따라서 기존의 배열을 변화시키는 `push`, `unshift`, `splice`, `pop` 등의 함수 대신 새로운 배열을 `return` 하는 `concat`, `slice`, `map`, `filter` 등의 함수를 사용해야 합니다. 또는 스프레드 문법을 통해 기존 배열 내용을 복사한 후 사용하는 것으로 불변성을 지킬 수 있습니다.
> 해당 [링크](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array#%EC%A0%91%EA%B7%BC%EC%9E%90_%EB%A9%94%EC%84%9C%EB%93%9C)에서 기존 배열을 기반해 새로운 배열을 반환하는 함수(접근자 메서드)를 확인할 수 있습니다.

## 1-1. 추가
### `concat` 함수 사용
```
const [users, setUsers] = useState(["Alice", "Bob", "Charley"]);

const onCreate = (createUser) => {
    setUsers(users.concat(createUser));
};
```
### 스프레드 문법 사용
```
const [users, setUsers] = useState(["Alice", "Bob", "Charley"]);

const onCreate = (createUser) => {
    setUsers([...users, createUser]);
};
```

## 1-2. 제거
### `filter` 함수 사용
```
const [users, setUsers] = useState(["Alice", "Bob", "Charley"]);

const onDelete = (deleteUser) => {
    setUsers(users.filter((user) => user !== deleteUser));
};
```
## 1-3. 수정
### `map` 함수 사용
```
const [users, setUsers] = useState(["Alice", "Bob", "Charley"]);

const onUpdate = (updateUser) => {
    setUsers(
        users.map((user) => (user === updateUser ? user + "★" : user))
    );
};
```
## 1-4. Immer
Immer는 React에서 사용할 수 있는 외부 라이브러리로 `state`를 변경할 때 새로운 배열을 생성하지 않아도 불변성을 유지할 수 있게 도와주는 라이브러리 입니다. 하지만 성능적으로 비교하였을 때 Immer를 사용한 코드의 동작 속도가 더 느리고 Immer를 사용하기 위해 필요한 기능이 환경에 따라 지원되지 않을 수 있다는 단점이 존재합니다. 따라서 Immer는 데이터의 구조가 복잡해져서 `state`를 변경할 코드가 복잡해질 때 이를 간단히 할 목적으로 사용하는 것이 권장됩니다.

### Immer 라이브러리 추가하기
```
$npm add immer
```

### Immer 라이브러리 사용하기
Immer 라이브러리는 주로 `produce` 이름으로 불러옵니다. `produce` 함수는 첫번째 파라미터에는 `state`, 두번째 파라미터에는 `state`를 변경할 함수를 넣어 사용할 수 있습니다. 두번째 파라미터에서 작성하는 함수는 불변성에 관계없이 작성할 수 있습니다.
```
import produce from "immer";

const [users, setUsers] = useState(["Alice", "Bob", "Charley"]);

const onCreate = (createUser) => {
    setUsers((users) =>
        produce(users, (draft) => {
            draft.push(createUser); // push 함수 사용
        })
    );
};
```
# 2. Context API
Context API를 사용하면 전역값을 설정해 여러 개의 컴포넌트를 거치지 않고 직접적으로 컴포넌트에 값을 전달할 수 있습니다. 따라서 매우 깊숙히 설정된 자식 컴포넌트에 값을 전달하기 위해 모든 부모 컴포넌트에 `props` 값을 전달해야하는 불필요한 과정을 생략할 수 있습니다.

## 2-1. Context API 사용하기
Context API는 생성 함수 `createContext`, 사용 함수 `useContext`, 값을 설정하는 `Provider`  컴포넌트를 통해 사용할 수 있습니다.

```
import React, { createContext, useContext } from "react";

const MyContext = createContext("Default Value"); // context 생성

function Child() {
  const text = useContext(MyContext); // context 사용
  return <div>{text}</div>;
}
function Parent() {
  return <Child />;
}
function GrandParent() {
  return <Parent />;
}

function ContextSample() {
  return (
    <MyContext.Provider value="Good">  // 생성한 context에 값 설정
      <GrandParent/>
    </MyContext.Provider>
  );
}

```
> GrandParent와 Parent 컴포넌트에 `props`로 값을 전달하지 않고도 Child 컴포넌트에서 값을 바로 설정할 수 있습니다.

# 3. Hooks 사용하기
Hooks은 React v16.8부터 새로 추가된 개념으로 기존에 클래스 컴포넌트에서만 `state`와 생명주기를 다룰 수 있는 것을 함수 컴포넌트에서도 다룰 수 있게 해주는 함수입니다. 현재 Hooks은 극히 드문 생명주기 관련 함수를 제외하고 대부분의 클래스 컴포넌트의 사용 사례를 커버할 수 있습니다.

## 3-1. useState
`useState`는 `state`를 다룰 수 있게 하는 Hook으로 `state`와 `setState`를 배열로 묶어 반환합니다.

```
function Counter(){
    const [count, setCount] = useState("Initial Value");

    const onIncrease = () => {
        setCount(count + 1); // 직접 변경
    }
    const onDecrease = () => {{
        setCount(count => count - 1); // callback 함수로 변경
    }}
    return (
        <button onClick={ onIncrease }>+1</button>;
        <button onClick={ onIncrease }>-1</button>;
    );
}
> `state`는 `setState` 함수에서 `state`를 직접 변경하거나 callback 함수를 통해 변경할 수 있다.
```
## 3-2. useRef
JS에서 특정 DOM을 선택하기 위해서는 `getElementByID`나 `querySelector` 함수를 사용합니다. React에서는 이와 같이 특정 DOM을 선택하기 위해서 `ref`와 `useRef`를 사용합니다. 

```
const resetBtn = useRef();

return (<button ref={resetBtn}>Reset</button>);
```
> `useRef`로 선택한 DOM은 `.current`를 통해 조작할 수 있습니다. (ex : resetBtn.current)

또한 `useRef`를 통해서 컴포넌트에서 조회, 수정할 수 있는 변수를 만들 수 있습니다. `state`를 통해 변경된 값을 참조하려면 `setState`로 변경 후 컴포넌트 리렌더링을 수행한 다음 `state`값을 참조할 수 있는 것과 달리 `useRef`로 만들어진 변수는 값이 변경되어도 컴포넌트의 리렌더링 없이 값을 사용할 수 있습니다. 
```
const nextId = useRef(4);

const onCreate = () => {
    // Create 로직

    nextId.current += 1;
};
```
## 3-3. useEffect
`useEffect` 함수는 대부분의 클래스 컴포넌트의 생명주기 메서드를 대체할 수 있는 함수로 컴포넌트가 렌더링된 이후 특정한 동작을 수행할 수 있게 하는 함수입니다. 즉, `useEffect`를 사용하면 함수로 선언된 컴포넌트의 변화를 쉽게 확인할 수 있고 그에 따른 동작을 손쉽게 부여할 수 있습니다. 
```
function Sample(){
    useEffect(() => {
        // 컴포넌트가 마운트될 때 수행할 동작

        return () => {
            // 컴포넌트가 언마운트될 때 수행할 동작
        }
    },[dependency]) // 내부에서 사용할 state나 props 속성 명시 -> 관련된 모든 컴포넌트에 대해 useEffect 동작 수행
}
```
> dependency 배열을 비우게되면 렌더링되는 모든 컴포넌트에 대해 `useEffect`함수가 호출됩니다.   

## 3-4. useMemo
`useMemo` 함수는 컴포넌트 렌더링의 성능 최적화를 위하여 주로 사용되는 함수로 이미 연산된 값(메모이제이션된 값)을 재사용하는 함수입니다. `useMemo` 함수를 사용하면 `dependency` 배열에 설정한 값이 변경되었을 때만 메모이제이션된 값을 재계산합니다.
```
const userCount = countUsers(users);

const userCount => useMemo(() => countUsers(users),[users]);
```
> `useMemo`를 사용하지 않는 위의 예제의 경우 컴포넌트가 렌더링되면 users의 수를 다시 계산합니다. 하지만 `useMemo`를 사용하는 아래 예제의 경우 컴포넌트가 렌더링되어도 users의 변화가 없다면 따로 계산을 수행하지 않고 기존에 계산된 값을 반환합니다.   

## 3-5. useCallback
`useCallback` 함수는 `useMemo`와 같이 컴포넌트 렌더링의 성능 최적화를 위해 사용되는 함수로 `useMemo`는 특정값을 재사용하는 것과 달리 `useCallback`은 특정 함수를 재사용하기 위해 사용하는 함수입니다. 값과 마찬가지로 컴포넌트가 리렌더링될 때 컴포넌트 내부의 함수도 새로 만들어지기 때문에 이로 인한 부하를 줄이기 위해 `useCallback` 함수를 사용합니다.
```
const onCreate = useCallback(() => {
    // Create 로직

}, [dependency])
```

## 3-6. useReducer()
`useReducer`함수는 함수형 컴포넌트에서 `state`를 다루는 또다른 방법입니다. `useReducer`는 `state`를 변경할 동작들을 관리하는 `reducer`함수, 해당 동작들을 연결할 `dispatch` 함수와 `action`객체를 사용합니다. `useReducer`를 사용하면 `state`를 변경하는 로직을 컴포넌트 외부에서 관리할 수 있습니다. 또한 `useReducer`는 `state`를 변경하기 위해 callback 함수 대신 `dispatch`를 사용하기 때문에 컴포넌트 성능을 최적화할 수 있습니다.
```
function reducer(state, action){ // 컴포넌트 외부에서 state 동작 관리
    switch(action.type){
        case 'INCREMENT': return { count: state.count + 1};
        case 'DECREMENT': return { count: state.count - 1};
        default: throw new Error();
    }
}

funtion Sample(){
    const [count, dispatch] = useReducer(reducer, initialState);

    const onIncrease = () => { dispatch({type: 'INCREMENT`}); };
    const onDecrease = () => { dispatch({type: 'DECREMENT`}); }; 
}

```
> `action` 객체에서는 `type` 속성으로 `state`를 관리할 동작을 구분하는 것이 권장됩니다.

# 4. 생명주기 메서드
생명주기 메서드는 클래스 컴포넌트에서 컴포넌트가 생성, 변경, 제거될 때 호출되는 메서드입니다. 생명주기 메서드를 사용하면 컴포넌트가 특정 상태에 있을 때 수행할 동작을 지정할 수 있습니다. 함수형 컴포넌트에서는 `useEffect` 함수를 사용해 대부분의 생명주기 메서드를 대체하여 사용할 수 있습니다.

### 컴포넌트 상태에 따라 호출되는 생명주기 메서드
- 컴포넌트 생성 : constructor → getDerivedStateFromProps → render → componentDidMount
- 컴포넌트 변경 : getDerivedStateFromProps → shouldComponentUpdate → render → getSnapshotBeforeUpdate → ComponentDidUpdate
- 컴포넌트 제거 : ComponentWillUnmount

## 4-1. constructor
`constructor` 메서드는 컴포넌트를 초기화하기 위해 호출하는 메서드로 `props` 초기화, `state` 초기화, 이벤트 메서드 바인딩 등을 수행합니다.

```
constructor(props) {
    super(props);
}
```
## 4-2. getDerivedStateFromProps
`getDerivedStateFromProps` 메서드는 컴포넌트를 처음으로 마운트하거나 변경할 때 render 메서드를 호출하기 직전에 호출되는 메서드입니다. `getDerivedStateFromProps` 메서드는 `props`의 값을 `state`에 반영하기 위해 사용됩니다. `getDerivedStateFromProps` 메서드에서 특정 객체를 반환하게 되면 해당 객체의 내용이 `state`에 반영됩니다.
```
getDerivedStateFromProps(props, state){
    if(props.userID !== state.userID){
        return { userID: props.userID }
    }
    return null;
}
```
## 4-3. render
`render` 메서드는 컴포넌트를 렌더링하는 메서드입니다. 컴포넌트를 구현하기 위해 필수적으로 구현되어야하는 메서드입니다.
```
render(){
    return <div></div>;
}
```
## 4-4. componentDidMount
`componentDidMount` 메서드는 컴포넌트가 브라우저에 마운트된 후 실행되는 메서드입니다. 따라서 `componentDidMount` 메서드가 실행되는 시점은 컴포넌트가 존재하고 있음이 보장되기 때문에 이 메서드 내에서 외부 API 연결, 네트워크 호출 등을 수행합니다.
```
componentDidMount(){ }
```
## 4-5. shouldComponentUpdate
`shouldComponentUpdate` 메서드에서는 변경될 `state`나 `props`값에 따라 컴포넌트 리렌더링 여부를 결정할 수 있습니다. 이 메소드에서 `false`를 반환하면 컴포넌트를 리렌더링 하지 않습니다.
```
shouldComponentUpdate(nextProps, nextState){
    return nextState.birthdayMonth % 2 === 0;
}
```
## 4-6. getSnapshotBeforeUpdate
`getSnapshotBeforeUpdate` 메서드는 리렌더링된 컴포넌트를 DOM에 마운트시키기 전 변경되기 전의 컴포넌트 정보를 얻을 수 있는 메서드입니다. 이 때 `return`되는 값은 `ComponentDidUpdate` 메서드의 `snapshot` 인자로 전달됩니다.
```
getSnapshotBeforeUpdate(prevProps, prevState){
    if (prevProps.username !== this.props.username) {
      return prevProps.username;
    }
    return null;
}
```
## 4-7. componentDidUpdate
`componentDidUpdate` 메서드는  리렌더링 컴포넌트를 DOM에 마운트시킨 후 호출되는 메서드입니다. `componentDidUpdate` 메서드에서는 변경된 컴포넌트에 대해 수행할 동작을 명시합니다. 이 때, `getSnapshotBeforeUpdate`에서 `return`한 `snapshot`값을 활용할 수 있습니다.
```
componentDidUpdate(prevProps, prevState, snapshot)
    if (this.props.userID !== prevProps.userID) {
        console.log(this.props.userID);
    
    console.log(`이전 사용자 : ${snapshot}`);
}
```
## 4-8. componentWillUnmount
`componentWillUnmount` 메서드는 컴포넌트가 언마운트되어 제거되기 직전에 실행되는 메서드입니다. 이 메서드 내에서 주로 등록된 이벤트 제거, 네트워크 요청 취소 등을 수행합니다.
```
componentWillUnmount(){ }
```