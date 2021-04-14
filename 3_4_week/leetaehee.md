# 1. Redux에 대하여
Redux는 웹 어플리케이션의 전체적인 상태(global state)를 다른 파일로 분리하여 한 번에 관리하기 위한 상태 관리 컨테이너입니다. Redux를 사용하면 상태 관리를 외부의 파일에서 수행하기 때문에 부모 컴포넌트를 통해 상태를 전달할 필요가 없습니다. 하지만 이와 같은 동작은 Context API를 통해 수행할 수 있지만, Redux가 Context API보다 우수한 점은 다음과 같습니다.

&lt;Redux의 장점&gt;
- 사용자의 액션 로그를 DevTools 등과 함께 사용하여 쉽게 관찰할 수 있다.
- 다른 기기의 다른 사용자들이 실행하는 액션을 로컬 작업과 병합하여 사용할 수 있다.
- 모든 상태를 특정 스토리지에 저장해 사용자가 재접속할 시 저장된 상태를 호출해 이전과 동일한 상태에서 시작할 수 있다.

## 1-1. redux, react-redux 설치
React에서 Redux를 사용하기 위해서는 `redux` 라이브러리와 React와 Redux를 연결하는 `react-redux` 라이브러리의 설치가 필요합니다.

```
$npm install redux react-redux
```

## 1-2. Redux의 기본 개념
### 1. Action
Action은 Store에 저장된 상태에 변화를 가하기 위해 사용할 데이터(객체)입니다. Action 객체는 `type` 필드를 필수적으로 가져야하며 객체형태로 사용하거나 생성함수의 형태로도 사용할 수 있습니다.

&lt;액션 객체 형태&gt;
```
{
  type: "ADD_TODO",
  text
}
```

&lt;액션 생성함수 형태&gt;
```
const addTodo = (text) => ({
  {
      type: "ADD_TODO",
      text
  }
});
```

### 2. Reducer
Reducer는 Action을 통해 전달한 데이터를 통해 Store에 저장된 상태에 변화를 가하는 행동을 정의하는 함수입니다. 여러 개의 Reducer를 합치기 위해서는 `combineReducers`라는 함수를 사용합니다.

&lt;Reducer 함수&gt;
```
function todoReducer(state=initialState, action){
    switch(action.type){
        case "ADD_TODO":
            return {...state, text: action.text}
        default:
            return state;
    }
}
export default todoReducer;
```
> Redux에서 Error를 throw하여 처리하지 않기 때문에 초기 상태 설정을 위해 state의 default 설정이 필요하다. (state=initialState)

&lt;여러 개의 Reducer 결합&gt;
```
import { combineReducers } from 'redux';
import counterReducer from './counter';
import todoReducer from './todo';

const rootReducer = combineReducers({
  counterReducer,
  todoReducer
});
```

### 3. Store
어플리케이션의 Action과 Reducer, 상태를 저장할 단 하나의 객체입니다. 생성된 Store에 이전에 만든 Reducer를 합치기 위해 `createStore` 함수를 사용합니다. 액션 객체나 액션 함수를 통해 Store의 상태에 접근하기 위해서는 dispatch를 사용합니다. 이후 선언한 Redux 모듈을 React와 연결하기 위해서는 `Provider` 컴포넌트를 사용합니다.

```
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import rootReducer from './modules';

const store = createStore(rootReducer);

store.dispatch(addTodo("Todo Item 1")); // addTodo 액션 사용

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

## 1-3. Redux 모듈 구현하기

&lt;counter.js&gt;
```
const INCREASE = "INCREASE";
const DECREASE = "DECREASE";

// Action 함수 선언
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });

const initialState = {
  number: 0,
};

// Reducer 선언
export default function counter(state = initialState, action) {
  switch (action.type) {
    case INCREASE:
      return {
        ...state,
        number: state.number + 1
      };
    case DECREASE:
      return {
        ...state,
        number: state.number - 1
      };
    default:
      return state;
  }
}
```

&lt;index.js&gt;
```
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import counter from './counter';

// store와 reducer 연결
const store = createStore(rootReducer);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

## 1-4. Redux 모듈 사용하기
Redux 모듈의 Store에 저장한 상태를 조회하기 위해서는 `react-redux` 라이브러리의 `useSelector` 함수를 사용할 수 있습니다. 또한 `useDispatch` 함수를 통해 Redux Store의 dispatch를 함수에서 사용할 수 있습니다.

&lt;Counter.js&gt;
```
function Counter({ number, onIncrease, onDecrease }) {
  return (
    <div>
      <h1>{number}</h1>
      <div>
        <button onClick={onIncrease}>+</button>
        <button onClick={onDecrease}>-</button>
      </div>
    </div>
  );
}
export default Counter;
```
&lt;CounterContainer.js&gt;
```
import { useSelector, useDispatch } from 'react-redux';

function CounterContainer() {
  // Redux 모듈에서 state Fetch
  const { number } = useSelector(state => ({
    number: state.counter.number,
  }));

  const dispatch = useDispatch();
  const onIncrease = () => dispatch(increase());
  const onDecrease = () => dispatch(decrease());

  return (
    <Counter
      number={number}
      onIncrease={onIncrease}
      onDecrease={onDecrease}
    />
  );
}
export default CounterContainer;
```
