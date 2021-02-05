> 이 글은 이재승님의 [실전 리액트 프로그래밍](https://www.inflearn.com/course/%EC%8B%A4%EC%A0%84-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/) 강좌를 듣고 공부하면서 필요한 부분을 정리한 글이다.
> [개인 블로그](https://jess2.xyz/react/react-tip-4/) 에도 같은 내용을 작성했습니다.

# 0. Index
1. [Context API](#1-context-api)
2. [ref 속성값으로 자식 요소에 접근하기](#2-ref-속성값으로-자식-요소에-접근하기)
3. [리액트 내장 훅](#3-리액트-내장-훅)

<br>

# 1. Context API
상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달할 때 속성값(Props)을 사용할 수 있다.
가까운 거리에 있는 몇 개의 컴포넌트로 전달할 때는 속성값으로도 충분하지만 많은 수의 하위컴포넌트로 전달할 때는 속성값을 내려주는 코드를 반복적으로 작성해야 한다.    
특히 멀리 떨어져 있는 컴포넌트에 데이터를 전달할 때는 중간에 있는 컴포넌트들에도 데이터를 전달하는 코드를 작성해야 한다.  

**Context API 를 사용하면 좀 더 간편하게 코드를 작성할 수 있다.**

<br>

### 1-1. Context API 사용하기 - Provider와 Comsumer

```jsx
import React, { createContext } from 'react';

const UserContext = createContext('unknown'); // 초기값을 넣어서 호출을 해주면 객체가 반환이 된다.

export default function App() {
  return (
    <div>
      <UserContext.Provider value="mike">
        <h1>메뉴</h1>
        <Profile />
      </UserContext.Provider>
    </div>
  )
}

function Profile() {
  return (
    <div>
      <Greeting />
    </div>
  )
}

function Greeting() {
  return (
    <UserContext.Consumer>
      {username => <p>{`${username}님 안녕하세요.`}</p>}  {/* mike님 안녕하세요. */}
    </UserContext.Consumer>
  )
}
```

- Context API를 사용하려면 react 에서 `createContext` 를 가져와야 한다.
- 초기값을 넣어서 호출을 해주면 **객체**가 반환이 되는데 **그 객체 안에는 Provider와 Consumer 컴포넌트가 있다.**
- Provider에서 `value`를 넣어주면 Consumer에서 그 값을 받아서 처리할 수 있다.
- Root 까지 올라갔는데 Provider를 찾지 못하면 기본값으로 설정한 `unknown`이 사용된다.
- Provider 컴포넌트의 `value` 값이 변경되면 하위의 모든 Consumer 컴포넌트는 다시 렌더링이 된다.

<br>

### 1-2. Consumer 말고 더 간편한 useContext 훅 이용하기

```js
function Greeting() {
    const username = useContext(UserContext);
    return <p>{`${username}님 안녕하세요.`}</p>
}
```

- useContext를 사용하면 훨씬 간편하게 Context API를 사용할 수 있다.

<br>

### 1-3. 하위 컴포넌트에서 데이터를 수정하는 방법 
- 데이터를 수정할 수 있는 함수를 별도의 Context로 만든다.

```js
import React, { createContext } from 'react';

const UserContext = createContext({ username: 'unknown', count: 0 });
const SetUserContext = createContext(() => {});

export default function App() {
  const [user, setUser] = useState({ username: 'jessie', count: 0 });

  return (
    <div>
      <SetUserContext.Provider value={setUser}>
        <UserContext.Provider value={user}>
          <Profile />
        </UserContext.Provider>
      </SetUSerContext.Provider>
    </div>
  )
}
```

```js
function Greeting() {
  const setUser = useContext(SetUserContext);
  const { username, count } = useContext(UserContext);

  return (
    <>
      <p>{`${username}님 안녕하세요.`}</p>
      <p>{`카운트 : ${count}`}</p>
      <button onClick={() => setUser({ username, count: count + 1 })}>
        인사하기
      </button>
    </>
  )
}
```

<br>

### 1-4. Context API를 사용할 때 주의할 점
1. 만약 `username`과 `count`를 별도의 상태값으로 관리하고 Provider에 `value={{ username, count }}` 이런식으로 넣으면 이 컴포넌트가 렌더링이 될 때마다 매번 새로운 객체가 만들어지게 된다. 그래서 `value`값이 변경되지 않아도 Consumer는 불필요하게 렌더링이 될 수 있다.  
`username`과 `count` 속성을 가진 `user`라는 객체를 상태값으로 관리하고 `value`에는 `value={user}` 이런 식으로 하나의 객체로 넘기면 매번 새로운 객체가 만들어지지 않는다.

2. Consumer 는 Provider 컴포넌트 안에 속해야 한다. Root 까지 올라갔는데 Provider를 찾지 못하면 기본값으로 설정된 값이 사용된다.   
보통 Provider는 루트에서 jsx 부분 전체를 감싸는 방식으로 많이 작성한다. 

<br>

# 2. ref 속성값으로 자식 요소에 접근하기 

**리액트로 작업하다보면 실제 돔 요소에 직접 접근해야할 때가 있다.**   
예를 들어, 돔 요소에 포커스를 주거나 돔 요소 크기나 스크롤 위치를 알아야 하는 경우 등등..  
`ref` 속성값을 이용하면 자식 요소에 직접 접근할 수 있다. 여기에서 자식 요소는 돔 요소나 컴포넌트일 수 있다.

### 2-1. 돔 요소에 접근하기

```js
export default function App() {
  const inputRef = useRef();

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return (
    <input type="text" ref={inputRef} />
  )
}
```

- useRef 훅을 사용한다.
- **current 라는 요소는 실제 돔 요소를 가리키게 된다.**
- 돔 요소는 렌더링 결과가 실제 돔에 반영된 후에 접근할 수가 있기 때문에 부수효과 함수 안에서 접근을 할 수 있다.
- `ref` 속성값은 아래와 같이 일반적인 컴포넌트에도 입력할 수 있다.

<br>

### 2-2. 컴포넌트에 접근하기

```js
<Box ref={inputRef} />
```

- 만약에 이 컴포넌트가 클래스형 컴포넌트라면 해당 컴포넌트의 인스턴스를 가리킨다. 따라서 `current` 속성은 해당 클래스의 메서드를 호출할 수 있게 된다.

- 함수형 컴포넌트는 인스턴스로 만들어지지 않지만 `useImperativeHandle` 이라는 훅을 사용하면 함수형 컴포넌트에서도 마치 클래스형 컴포넌트의 멤버 변수나 메서드에 접근하는 것처럼 함수형 컴포넌트의 변수나 함수를 외부로 노출시킬 수 있다.

- **주의: 별다른 처리를 하지 않았다면 함수형 컴포넌트에 `ref` 속성을 입력할 수 없고 아래와 같이 별도의 속성명을 사용하거나 forwardRef 를 사용해야 한다.**

    - 별도의 속성명 사용하기
        ```js
        export default function App() {
          const inputRef = useRef();
          
          useEffect(() => {
            inputRef.current.focus();
          }, []);
        
          return (
            <div>
              <InputAndSave inputRef={inputRef} />
              ...
            </div>
          )
        }
        
        function InputAndSave({ inputRef }) {
          return (
            <div>
              <input type="text" ref={inputRef} />
              ...
            </div>
          )
        }
        ```
    - forwardRef 사용하기 : 두 번째 매개변수로 ref 속성값을 받을 수 있다.
        ```js
        export default function App() {
          const buttonRef = useRef();
        
          return (
            <div>
              <Button ref={buttonRef} />
              ...
            </div>
          )
        }
        
        const Button = React.forwardRef(function ({ onClick }, ref) {
          return (
            <button onClick={onClick} ref={ref}>저장</button>
          )
        })
        ```

<br>

### 2-3. ref 속성값에 함수 입력하기
```js
export default function App() {
  const [text, setText] = useState(INITIAL_TEXT);
  const [showText, setShowText] = useState(true);
  
  return (
    <div>
      {showText && (
        <input
          type="text"
          ref={ref => ref && setText(INITIAL_TEXT)}
          value={text}
          onChange={e => setText(e.target.value)}
        />
      )}   
      <button onClick={() => setShowText(!showText)}>보이기/가리기</button>
    </div>
  )
}

const INITIAL_TEXT = 'Hello world!';
```
- useRef 속성을 사용하지 않고 `ref` 속성값에 함수를 입력할 수 있다.
- ref 속성값에 입력된 함수는 해당하는 요소가 생성되거나 사라질 때 한 번씩 호출된다.
- 생성될 때는 해당하는 요소의 레퍼런스가 넘어온다.
- 사라질 때는 `null` 값이 넘어온다.
- 따라서 요소가 생성될 때 `setText(INITIAL_TEXT)` 를 실행하여 `text`를 초기값으로 설정한다.
- 이 코드에서는 버튼을 클릭할 때마다 `showText`를 보였다가 가렸다가를 반복하게 되는데, `input` 태그는 `showText`가 `true`일 때만 보여지게 되므로 버튼을 클릭하여 `showText`가 `true`가 되는 순간 `setText(INITIAL_TEXT)` 함수가 호출된다.
- 문자를 입력할 때마다 `setText` 함수가 실행되고 컴포넌트가 다시 렌더링이 된다. 그런데 컴포넌트가 렌더링될 때마다 새로운 함수가 입력되면서 `setText(INITIAL_TEXT)`가 실행되어 초기화되기 때문에 `text`가 입력하는 텍스트로 제대로 업데이트 되지 않는 문제가 있다.

<br>

### 2-4. useCallback 훅을 사용하여 함수를 고정하기
```js
export default function App() {
  const [text, setText] = useState(INITIAL_TEXT);
  const [showText, setShowText] = useState(true);

  const setInitialText = useCallback((ref) => {
    ref && setText(INITIAL_TEXT)
  }, []);
  
  return (
    <div>
      {showText && (
        <input
          type="text"
          ref={setInitialText}
          value={text}
          onChange={e => setText(e.target.value)}
        />
      )}   
      <button onClick={() => setShowText(!showText)}>보이기/가리기</button>
    </div>
  )
}

const INITIAL_TEXT = 'Hello world!';
```
- useCallback 훅의 메모이제이션 기능 덕분에 한 번 생성된 함수를 변화시키지 않고 계속해서 재사용할 수 있다.
- 따라서 문자를 입력할 때마다 `setText` 함수가 실행되고 컴포넌트가 다시 렌더링 되어도 `setText(INITIAL_TEXT)`가 다시 실행되지 않는다. 
- `ref`가 새로 생성될 때 `setText(INITIAL_TEXT)`가 실행된다.

<br>

# 3. 리액트 내장 훅

1. useState - 상태값 관리 [설명](https://jess2.xyz/react/react-tip-3/#1-2-usestate--%EC%83%81%ED%83%9C%EA%B0%92-%EA%B4%80%EB%A6%AC-hook)
2. useEffect - 부수효과 처리 [설명](https://jess2.xyz/react/react-tip-3/#1-3-useeffect--%EB%B6%80%EC%88%98-%ED%9A%A8%EA%B3%BC-%EC%B2%98%EB%A6%AC-hook)
3. useContext - Context API Consumer 기능 사용 [설명](https://jess2.xyz/react/react-tip-4/#1-context-api)
4. useRef - 자식 요소에 직접 접근 등 [설명1](https://jess2.xyz/react/react-tip-4/#2-ref-%EC%86%8D%EC%84%B1%EA%B0%92%EC%9C%BC%EB%A1%9C-%EC%9E%90%EC%8B%9D-%EC%9A%94%EC%86%8C%EC%97%90-%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0)
                               [설명2](https://jess2.xyz/react/react-tip-4/#3-1-useref)
5. useMemo - 메모이제이션 기능 [설명](https://jess2.xyz/react/react-tip-4/#3-2-usememo)
6. useCallback - 함수 메모이제이션 기능에 특화됨 [설명](https://jess2.xyz/react/react-tip-4/#3-3-usecallback)
7. useReducer - 여러 개의 상태값 관리 [설명](https://jess2.xyz/react/react-tip-4/#3-4-usereducer)
8. useImperativeHandle
9. useLayoutEffect
10. useDebugValue

<br>

### 3-1. useRef
> 렌더링과 상관 없는 데이터를 저장할 때 useRef가 유용하게 사용될 수 있다.

#### 타이머
```js
export default function App() {
  const timerIdRef = useRef(-1);
  useEffect(() => {
    timerIdRef.current = setTimeout(() => {}, 1000);
  });
  useEffect(() => {
    if (timerIdRef.current >= 0) {
      clearTimeout(timerIdRef.current);
    } 
  });
}
```
- 만약 useState를 사용한다면 불필요한 렌더링이 일어날 수 있다.

<br> 

#### 이전 상태값을 기억
```js
export default function App() {
  const [age, setAge] = useState(20);
  const prevAgeRef = useRef(20);
  
  useEffect(() => {
    prevAgeRef.current = age;
  }, [age]);
  
  const prevAge = prevAgeRef.current;
  const text = (age === prevAge) ? ('same') : (age > prevAge ? 'older' : 'younger');

  return (
    // ...
    // 버튼을 누르면 setAge 함수를 호출하여 랜덤값으로 age를 변경한다.
  );
}
```
- 이전 `age` 값을 기억하기 위해서 `prevAgeRef`를 사용한다.
- useEffect훅은 렌더링된 후에 호출이 된다. 따라서 `prevAge.current`는 `age`가 변경되기 이전의 값을 기억하고 있게 되는 것이다.
- 예시 과정
    - `setAge` 호출 -> `age`가 초기값 20에서 28로 변경됨 -> 렌더링 -> 이 때 `prevAgeRef.current`는 20임 -> 화면에는 `age`로 28이 출력되고 `prevAge`로 20이 출력됨 -> `prevAgeRef.current`에 28을 저장
    -> `setAge` 호출 -> `age`가 19로 변경됨 -> 렌더링 -> 이 때 `prevAgeRef.current`는 28임 -> 화면에는 `age`로 19가 출력되고 `prevAge`로 28이 출력됨 -> ... 반복

<br>

### 3-2. useMemo
> 메모이제이션 기능이 있어, 계산량이 많은 함수의 반환값을 재활용하는 용도로 사용된다.

```js
export default function App() {
  const [v1, setV1] = useState(0);
  const [v2, setV2] = useState(0);
  const [v3, setV3] = useState(0);

  const value = useMemo(() => {
    runExpensiveJob(v1, v2); // 계산량이 많은 함수 호출
  }, [v1, v2]);

  return (
    //...
  );
}
```
- useMemo 훅의 첫 번째 매개변수로 함수를 입력하면 이 함수가 실행된 결과값을 리액트가 기억한다.
- useMemo 훅의 두 번째 매개변수인 배열에 있는 값이 하나라도 변경되면, 첫 번째 매개변수로 입력된 함수가 실행된다.
- 만약 두 번째 매개변수인 배열에 있는 값이 변경되지 않으면, 이전에 실행해서 기억하고 있던 결과값을 재활용한다.
- useEffect 훅과 마찬가지로 이 두 번째 매개변수인 배열은 **의존성 배열**이다.
- 위의 코드에서는 `v1`이나 `v2`가 변경되면 `runExpensiveJob`함수가 실행되지만 `v3`가 변경되면 `runExpensiveJob`함수가 실행되지 않는다.

<br>

### 3-3. useCallback
> useMemo와 마찬가지로 메모이제이션 기능이 있으며, 함수 메모이제이션에 특화된 훅이다.

#### 불필요하게 함수가 다시 생성되는 문제 

```js
export default function App() {
  const [name, setName] = useState('');
  
  return (
    <div>
      <p>{`name is ${name}`}</p>
      <UserEdit
        onSave={() => saveToServer(name)}
        setName={setName}
      />
    </div>
  );
}

const UserEdit = React.memo(function ({ onSave, setName }) {
  //...
});
```

- `onSave={() => saveToServer(name)}` : 자식 컴포넌트에 함수를 입력해서 속성값으로 전달할 때는 App 컴포넌트가 렌더링될 때마다 새로운 함수가 생성되어 입력이 된다. 이러면 App 컴포넌트가 렌더링될 때마다 
매번 `onSave` 속성값이 변경되기 때문에, 자식 컴포넌트인 UserEdit 컴포넌트 입장에서는 불필요하게 새로운 속성값으로 입력받고 불필요하게 다시 렌더링된다.
- React.memo를 사용하더라도 이러한 문제가 발생하는데 useCallback 훅을 사용하면 이 문제를 해결할 수 있다.

<br>

#### useCallback으로 해결
```js
export default function App() {
  const [name, setName] = useState('');
  const onSave = useCallback(() => {
    saveToServer(name);
  }, [name]);
  
  return (
    <div>
      <p>{`name is ${name}`}</p>
      <UserEdit
        onSave={onSave}
        setName={setName}
      />
    </div>
  );
}

const UserEdit = React.memo(function ({ onSave, setName }) {
  //...
});
```
- useCallback 훅도 의존성 배열로 관리한다.
- 위의 코드에서는 `name`이 변경될 때만 새로운 함수가 생성된다.

<br>

### 3-4. useReducer
> useState와 비슷하지만 여러 개의 상태값 관리할 때 사용하면 좋은 훅

```js
export default function App() {
  const [state, dispatch] = useReducer(reducer, INITIAL_STATE);

  return (
    <div>
      <p>{`name is ${state.name}`}</p>
      <p>{`age is ${state.age}`}</p>
      <input
        type='text'
        value={state.name}
        onChange={e => dispatch({ type: 'setName', name: e.currentTarget.value})}
      />
      <input
        type='number'
        value={state.age}
        onChange={e => dispatch({ type: 'setAge', age: e.currentTarget.value})}
      />
    </div>
  );
}
```
- useReducer는 useState와 마찬가지로 배열로 반환이 된다.
- 배열의 첫 번째 원소 : 상태값
- 배열의 두 번째 원소 : 상태값을 변경할 수 있는 dispatch 함수
- 첫 번째 매개변수 : reducer 함수
- 두 번째 매개변수 : 초기 상태값

#### reducer 함수
```js
function reducer(state, action) {
  switch(action.type) {
    case 'setName':
      return { ...state, name: action.name };
    case 'setAge':
      return { ...state, age: action.age };
    default:
      return state;
  }
}
```
- `action`을 보고 상태값을 어떻게 변경할지 판단한다.

<br>

### 3-5. useReducer와 Context API
> 보통 상위 컴포넌트에서 다수의 상태값을 관리한다.
이 때 자식 컴포넌트에서 발생한 이벤트에서 상위 컴포넌트의 상태값을 변경해야 하는 경우가 많다.
이를 위해서 상위 컴포넌트에서 트리의 깊은 곳까지 이벤트 처리 함수를 전달하기도 하는데 이는 비효율적이다.  
useReducer 훅과 Context API를 같이 이용하면 쉽게 전달할 수 있다.

```js
export const ProfileDispatch = React.createContext(null);

export default function App() {
  const [state, dispatch] = useReducer(reducer, INITIAL_STATE);

  return (
    <div>
      <p>{`name is ${state.name}`}</p>
      <p>{`age is ${state.age}`}</p>
      
      <ProfileDispatch.Provider value={dispatch}>
        <SomeComponent />
      </ProfileDispatch.Provider>
    </div>
  );
}
```
- 위 코드에서는 createContext를 이용해서 `ProfileDispatch`라는 Context를 만들었다.
- App 컴포넌트에서 Provider를 이용해서 `value`로 useReducer의 `dispatch` 함수를 내려준다.
- 그러면 필요한 컴포넌트에서 useContext를 이용해서 `dispatch` 함수를 사용할 수 있다.


