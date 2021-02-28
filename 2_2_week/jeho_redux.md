# 리덕스를 사용하여 리액트 애플리케이션 상태 관리하기

[프레젠테이셔널, 컨테이너 컴포넌트 패턴](#프레젠테이셔널,-컨테이너-컴포넌트-패턴)

# 프레젠테이셔널 컴포넌트

프레젠테이셔널 컨포넌트가 될 UI 컴포넌트 두 개를 준비한다

- +,-버튼으로 동작하는 Counter 컴포넌트
- 등록, 삭제, 체크가 가능한 Todos 컴포넌트

<img width="294" alt="image" src="https://user-images.githubusercontent.com/45571631/109408518-d85a8680-79cd-11eb-89f9-44f8bdfa49fb.png">


<details markdown="1">

<summary>Presentatinal 코드들</summary>

```jsx
import React from 'react';
import TodoItem from "./TodoItem";

const Todos = ({
    input,
    todos,
    onChangeInput,
    onInsert,
    onToggle,
    onRemove,
               }) => {
    function onSubmit(e) {
        e.preventDefault();
    }

return (
    <div>
        <form action="submit" onSubmit={onSubmit}>
            <input type="text"/>
            <button type={"submit"}>등록</button>
        </form>
        <div>
            <TodoItem/>
            <TodoItem/>
            <TodoItem/>
            <TodoItem/>
            <TodoItem/>
        </div>
    </div>
);
};  

export default Todos;
```

```jsx
import React from "react";

const TodoItem = ({todo, onToggle, onRemove}) => {
    return (
        <div>
            <input type="checkbox"/>
            <span>예제 텍스트</span>
            <button>삭제</button>
        </div>
    );
};

export default TodoItem;
```

```jsx
import React from 'react';

const Counter = ({number, onIncrease, onDecrease}) => {
    return (
        <div>
            <h1>{number}</h1>
            <div>
                <button onClick={onIncrease}>+1</button>
                <button onClick={onDecrease}>-1</button>
            </div>
        </div>
    );
};

export default Counter;
```

</details>

# 리덕스 코드 작성하기


[Redux의 파일 구조](#redux의-파일-구조)

<details markdown="1">
<summary>counter Module</summary>

```jsx
const INCREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';

export const increase = () => ({type: INCREASE});
export const decrease = () => ({type: DECREASE});

const initialState = {
    number : 0
};

function counter(state = initialState, action) {
    switch (action.type){
        case INCREASE :
            return {
                number: state.number+1
            };
        case DECREASE :
            return {
                number: state.number-1
            };
        default :
            return state;
    }
}

export default counter;
```

</details>


<details markdown="1">
<summary>todos Module</summary>

```jsx
const CHANGE_INPUT = 'todos/CHANGE_INPUT';
const INSERT = 'todos/INSERT';
const TOGGLE = 'todos/TOGGLE';
const REMOVE = 'todos/REMOVE';

export const changeInput = (input)=> ({
    type:CHANGE_INPUT,
    input
});
let id = 3;
export const insert = (text)=> ({
    type:INSERT,
    todo: {
        id: id++,
        text,
        done: false,
    }
});
export const toggle = ()=> ({
    type:TOGGLE,
    id
});
export const remove = ()=> ({
    type:REMOVE,
    id
});

const initialState = {
    input : '',
    todos : [
        {
            id:1,
            text: '리덕스 기초 배우기',
            done : true,
        },
        {
            id:2,
            text: '리액트와 리덕스 사용하기',
            done : false
        }
    ]
};
function todos(state = initialState, action) {
    switch (action.type) {
        case CHANGE_INPUT :
            return {
                ...state,
                input : action.input
            };
        case INSERT :
            return {
                ...state,
                todos : state.todos.concat(action.todo)
            }
        case TOGGLE :
            return {
                ...state,
                todos : state.todos.map(todo=> (todo.id===action.id) ? {...todo, done: !todo.done} : todo)
            }
        case REMOVE :
            return {
                ...state,
                todos : state.todos.filter(todo=> (todo.id!==action.id))
            }
        default : return state;
    }
}

export default todos;
```

</details>


### 루트 리듀서 만들기

store를 만들 때 파라미터로 들어가는 리듀서는 하나인데 현재 만든 리듀서 함수는 두 개 이므로combineReducers를 이용하여 리듀서를 하나로 합쳐준다

```jsx
import counter from "./counter";
import todos from "./todos";
import {combineReducers} from "redux";

const rootReducer = combineReducers({
    counter,
    todos
});

export default rootReducer;
```

# 리덕스 적용하기

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import {createStore} from "redux";
import rootReducer from "./modules";
import {Provider} from "react-redux";

const store = createStore(
    rootReducer,
		// redux Dev_Tools를 이용하기 위한 코드
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
    );

ReactDOM.render(
    <Provider store={store}>
    <App />
    </Provider>,
  document.getElementById('root')
);
```

이전에 하나로 합친 리듀서를 스토어에 파라미터로 넘겨줘서 프로젝트에서 사용할 store를 만든 뒤 

Provider를 이용해서 App을 감싸주면 이제 프로젝트에서 리덕스를 사용할 준비가 끝났다

# 컨테이너 컴포넌트

이제 Counter 컴포넌트에 props를 줄 CounterContainer 컴포넌트를 작성해야한다

위에서 작성한 store에 저장되어있는 데이터들을 CounterContainer에서 조회하려면 어떻게 해야할까?

## connect

react-redux에서 제공하는 connect 함수를 이용하는 방법이 있다

```jsx
connect(mapStateToProps, mapDispatchToProps)(Component);
```

connect 함수는 위와 같이 두 개의 함수를 파라미터로 받고 연동할 컴포넌트를 파라미터로 받는 함수를 반환한다

위 코드는 아래와 같다

```jsx
// store에서 state와 dispatch를 조회
const fn = connect(mapStateToProps, mapDispatchToProps);
// 위에서 조회한 store의 state와 dispatch들을 원하는 component에 연동
fn(Component);
```

위와 같은 코드를 작성해주고 나면 이제 Component에서 store의 state와 dispatch 조회가 가능하다

### mapStateToProps

mapStateToProps는 store의 state를 파라미터로 받은 뒤 객체를 반환하는데

이 때 반환한 객체는 연동할 컴포넌트의 props로 전달된다

```jsx
const mapStateToProps = (state)=> ({
		number : state.counter.number
});
```

### mapDispatchToProps

mapDispatchToProps도 비슷하게 store의 내장 함수인 dispatch를 파라미터로 받은 뒤 객체를 반환한다

마찬가지로 객체는 연동할 컴포넌트의 props로 전달된다

```jsx
const mapDispatchToProps = (dispatch)=> ({
		// 여기서 사용하는 increase()와 decrease()는
		// import {decrease, increase} from "../modules/counter";
		// modules에서 작성한 액션 객체 생성 함수이다
    increase : ()=> dispatch(increase()),
    decrease : ()=> dispatch(decrease()),
});
```

전달된 increase와 decrease는 컴포넌트 내부에서 호출될 시

1. 액션 객체 생성 함수 실행
2. 1에서 생성한 객체를 dipatch의 파라미터로 전달
3. dispatch가 호출되어 리듀서 함수 실행
4. 액션에 맞는 동작 실행

### connect

위에서 connect 함수에 필요한 파라미터들을 다 작성했으니 다음과 같이 연동하면 된다

```jsx
export default connect(mapStateToProps,mapDispatchToProps)(CounterContainer);
```

연동과 동시에 CounterContainer를 내보냄으로써 state와 dispatch가 CounterContainer의 props로 전달되었다

---


완성된 CounterContainer와 Counter 컴포넌트는 다음과 같다

```jsx
// CounterConatiner.js
import React from 'react';
import Counter from "../components/Counter";
import {connect} from "react-redux";
import {decrease, increase} from "../modules/counter";

const CounterContainer = ({number, decrease, increase}) => {
    return (
        <div>
            <Counter number={number} onDecrease={decrease} onIncrease={increase}/>
        </div>
    );
};
const mapStateToProps = state => ({
    number : state.counter.number,
})

const mapDispatchToProps = (dispatch)=> ({
    increase : ()=> dispatch(increase()),
    decrease : ()=> dispatch(decrease()),
})
export default connect(mapStateToProps,mapDispatchToProps)(CounterContainer);
```

```jsx
// Counter.js
import React from 'react';

const Counter = ({number, onIncrease, onDecrease}) => {
    return (
        <div>
            <h1>{number}</h1>
            <div>
                <button onClick={onIncrease}>+1</button>
                <button onClick={onDecrease}>-1</button>
            </div>
        </div>
    );
};

export default Counter;
```

이로써 

Counter는 데이터가 어떻게 처리되는지 전혀 알지 못하고 뷰만 관리하고 있고 

⇒ Presentational

ContainerCounter는 뷰가 어떻게 보이는지 전혀 알지 못하고 데이터 관리와 전달을 담당하고 있으며 

⇒ Container

데이터를 처리하는 로직은 redux 관련 코드들이 처리하고 있다

⇒ modules

# Hooks 사용하기

connect함수를 이용하여 store에 있는 데이터들을 조회했지만 react-redux에서 제공하는 Hook들을 사용할 수도 있다

## useSelector

useSelector는 connect함수의 첫 번째 파라미터였던  mapStateToProps와 동일한 역할을 하고 형태 또한 같다

```jsx
const CounterContainer = (decrease, increase) => {
    const number = useSelector(state => state.counter.number);
    return (
        <div>
            <Counter number={number} onDecrease={decrease} onIncrease={increase}/>
        </div>
    );
}
```

useSelector로 number를 조회했기 때문에 connect로 받아오던 props의 number는 이제 필요가 없어졌다

## useDispatch

mapStateToProps를 대체 했으니 이번엔 mapDispatchToProps의 차례다

useDispatch를 사용하면 connect를 사용하지않고 컴포넌트 내부에서 dispatch를 발생시킬 수 있다

```jsx
const CounterContainer = () => {
    const number = useSelector(state => state.counter.number);
    const dispatch = useDispatch();
    return (
        <div>
            <Counter number={number} onDecrease={()=>dispatch(decrease())} onIncrease={()=>dispatch(increase())}/>
        </div>
    );
};
```

---
---


# 프레젠테이셔널, 컨테이너 컴포넌트 패턴

리액트 프로젝트에서 리덕스를 사용할 때 가장 많이 사용하는 패턴이다

view를 책임지는 Presentational Component와 동작을 책임지는 Container Component로 나누어서 재사용과 유지 보수성을 높이는데 도움을 준다

두 개의 컴포넌트들은 각각 다음과 같은 특성을 가진다

### Presentational Component

- 어떻게 보여질지를 책임진다
- 내부에 Presentational Component와 Container Component 모두 가질 수 있다
- 대부분 DOM 마크업과 스타일을 가지고 있다
- 어플리케이션의 나머지 부분에 아무런 의존성을 가지지 않는다
- 데이터를 건드리는 작업은 일체하지 않고 필요한 데이터는 props로 받아오기만 한다
- UI상태를 관리하기 위해 state를 가질 수 있다

### Container Component

- 어떻게 동작할 지를 책임진다
- 내부에 Presentational Component와 Container Component 모두 가질 수 있다
- 대부분 보여주기 위한 DOM 마크업이나 스타일은 가지고 있지 않다
- 데이터를 다른 컴포넌트에게 제공한다
- state를 활용한 데이터 저장소로 쓰인다
- 직접 작성되기 보다는 고차 컴포넌트에 의해 생성되는 경우가 많다

이 두 성질을 가진 컴포넌트들은 명확히 구분되어야 하기 때문에 보통 다른 디렉토리에 저장된다

## 장점

- 관심사의 분리가 쉽다

    ⇒ 컴포넌트를 두 가지로 분류한 만큼 UI와 데이터의 관심사 분리가 쉽다

- 재사용성을 높일 수 있다

    ⇒ 다른 Container Component 에서 온 데이터라 할지라도 같은 Presentational Component로 재사용하여 표현할 수 있다
    

# Redux의 파일 구조

# 기존의 Redux 파일 구조

리덕스 공식 문서에 있는 todo app의 파일 구조를 예시로 살펴보자

<img width="224" alt="todoapp" src="https://user-images.githubusercontent.com/45571631/109408519-d8f31d00-79cd-11eb-8d3c-9bb5fc9d37e4.png">

presentationer, container components 패턴을 활용하고 있는 component, contiainer 폴더를 제외하면 

- actions
- constants
- reducers

3가지로 컴포넌트들을 분류한 걸 볼 수 있다

### constants

constants 폴더에는 액션 객체를 생성하기 위한 상수들이 저장되어 있다

```jsx
// in constants/ActionTypes.js
export const ADD_TODO = 'ADD_TODO'
export const DELETE_TODO = 'DELETE_TODO'
export const EDIT_TODO = 'EDIT_TODO'
export const COMPLETE_TODO = 'COMPLETE_TODO'
export const COMPLETE_ALL_TODOS = 'COMPLETE_ALL_TODOS'
export const CLEAR_COMPLETED = 'CLEAR_COMPLETED'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'
```

### actions

actions 폴더의 index에서는 ActionTypes를 import해서 액션 객체 생성 함수를 관리하고 있다

```jsx
// in actions/index.js
import * as types from '../constants/ActionTypes'

export const addTodo = text => ({ type: types.ADD_TODO, text })
export const deleteTodo = id => ({ type: types.DELETE_TODO, id })
export const editTodo = (id, text) => ({ type: types.EDIT_TODO, id, text })
export const completeTodo = id => ({ type: types.COMPLETE_TODO, id })
export const completeAllTodos = () => ({ type: types.COMPLETE_ALL_TODOS })
export const clearCompleted = () => ({ type: types.CLEAR_COMPLETED })
export const setVisibilityFilter = filter => ({ type: types.SET_VISIBILITY_FILTER, filter})
```

### reducers

reducers 폴더의 index에서는 액션에 따라서 어떻게 동작할지가 구현되어있다

```javascript
//in reducers/index.js
import {
  ADD_TODO,
  DELETE_TODO,
  EDIT_TODO,
  COMPLETE_TODO,
  COMPLETE_ALL_TODOS,
  CLEAR_COMPLETED
} from '../constants/ActionTypes'

const initialState = [
  {
    text: 'Use Redux',
    completed: false,
    id: 0
  }
]

export default function todos(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          id: state.reduce((maxId, todo) => Math.max(todo.id, maxId), -1) + 1,
          completed: false,
          text: action.text
        }
      ]

    case DELETE_TODO:
      return state.filter(todo =>
        todo.id !== action.id
      )

    case EDIT_TODO:
      return state.map(todo =>
        todo.id === action.id ?
          { ...todo, text: action.text } :
          todo
      )

    case COMPLETE_TODO:
      return state.map(todo =>
        todo.id === action.id ?
          { ...todo, completed: !todo.completed } :
          todo
      )

    case COMPLETE_ALL_TODOS:
      const areAllMarked = state.every(todo => todo.completed)
      return state.map(todo => ({
        ...todo,
        completed: !areAllMarked
      }))

    case CLEAR_COMPLETED:
      return state.filter(todo => todo.completed === false)

    default:
      return state
  }
}
```

### 불편함

이러한 구조로 redux를 사용하다 보면 기능을 추가하거나 수정할 일이 생겼을 때 3가지 파일을 모두 건드려야 하는 상황이 생기는데

이러한 불편함을 개선하고자 해서 나온 것이 바로 ducks 패턴이다

# Ducks 패턴

리덕스를 사용할 때 기능별로 하나의 파일에 액션 객체 생성자, 액션 상수, 리듀스 함수 관련 코드를 작성하고 모듈 폴더에서 관리하는 파일 구조를 말한다

## 규칙

Ducks 패턴을 사용할 때는 다음과 같은 규칙을 지켜야한다

1. **항상** `reducer()` 라고 불리는 reducer함수를 export해야한다
2. **항상** 모듈의 action 객체 생성 함수를 `export` 해야한다
3. action객체의 이름은 **항상** `/해당 reducer 이름/ACTION_TYPE` 형태여야 한다
4. **어쩌면** action 타입들을 `UPPER_SNAKE_CASE`로 `export` 할 수 있다

위의 규칙을 따라서 아까 봤던 todo 예제를 하나의 module로 만들면 다음과 같다

```jsx
// modules/todos.js
const ADD_TODO = 'todos/ADD_TODO'
const DELETE_TODO = 'todos/DELETE_TODO'
const EDIT_TODO = 'todos/EDIT_TODO'
const COMPLETE_TODO = 'todos/COMPLETE_TODO'
const COMPLETE_ALL_TODOS = 'todos/COMPLETE_ALL_TODOS'
const CLEAR_COMPLETED = 'todos/CLEAR_COMPLETED'
const SET_VISIBILITY_FILTER = 'todos/SET_VISIBILITY_FILTER'

export const addTodo = text => ({ type: types.ADD_TODO, text })
export const deleteTodo = id => ({ type: types.DELETE_TODO, id })
export const editTodo = (id, text) => ({ type: types.EDIT_TODO, id, text })
export const completeTodo = id => ({ type: types.COMPLETE_TODO, id })
export const completeAllTodos = () => ({ type: types.COMPLETE_ALL_TODOS })
export const clearCompleted = () => ({ type: types.CLEAR_COMPLETED })
export const setVisibilityFilter = filter => ({ type: types.SET_VISIBILITY_FILTER, filter})

const initialState = [
  {
    text: 'Use Redux',
    completed: false,
    id: 0
  }
]

export default function reducer(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          id: state.reduce((maxId, todo) => Math.max(todo.id, maxId), -1) + 1,
          completed: false,
          text: action.text
        }
      ]

    case DELETE_TODO:
      return state.filter(todo =>
        todo.id !== action.id
      )

    case EDIT_TODO:
      return state.map(todo =>
        todo.id === action.id ?
          { ...todo, text: action.text } :
          todo
      )

    case COMPLETE_TODO:
      return state.map(todo =>
        todo.id === action.id ?
          { ...todo, completed: !todo.completed } :
          todo
      )

    case COMPLETE_ALL_TODOS:
      const areAllMarked = state.every(todo => todo.completed)
      return state.map(todo => ({
        ...todo,
        completed: !areAllMarked
      }))

    case CLEAR_COMPLETED:
      return state.filter(todo => todo.completed === false)

    default:
      return state
  }
}
```

