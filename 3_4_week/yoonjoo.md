# 이번주에 한 일 : Redux에 대한 튜토리얼을 읽어보았습니다.

## Redux
그동안 연습했던 리액트 기초 튜토리얼에서는 자식 컴포넌트가 그리 많지 않았습니다. 
그래서 App.js에서 컴포넌트들을 만들고, 각 컴포넌트에서 뭔가 변화가 일어나면 그 내용을 부모컴포넌트인 App.js에 전달해서 필요한 내용을 업데이트하고 다시 렌더링해도 괜찮았습니다.
그런데 자식컴포넌트가 많아지고 데이터도 늘어나고, 데이터를 업데이트해야할 경우가 많아지고 하면 일일이 자식 컴포넌트들을 한차례 한차례 거쳐서 내용을 전달하는 게 번거롭기도 하고, 
시스템을 유지하고 보수하기가 까다로워질 수 있습니다. 그래서 상태변화를 쉽게 관리하기 위해 리덕스를 쓴다고 합니다.

## 개념들
아래 링크들을 참고했습니다. 
+ https://redux.js.org/tutorials/essentials/part-1-overview-concepts 
+ https://velog.io/@velopert/Redux-1-%EC%86%8C%EA%B0%9C-%EB%B0%8F-%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC-zxjlta8ywt

### Action 
"An action is a plain JavaScript object that has a type field. 
You can think of an action as an event that describes something that happened in the application."
라고 써있네요. 예를 들어서 

```
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```
이렇게 쓰게 되는데 type에다가는 "함수가 속한 카테고리/액션에 대한 설명" 이런 식으로 string을 쓰게 된다고 합니다. payload처럼 추가로 필드를 가질 수도 있고요.

### Action Creators
Action 객체를 만드는 함수입니다. 
```
function addTodo(data) {
  return {
    type: "ADD_TODO",
    data
  };
}
```
이렇게 만들수도 있고, 아래처럼 화살표 함수로 만들 수도 있습니다. 
```
const changeInput = text => ({ 
  type: "CHANGE_INPUT",
  text
});
```

### Reducers
"A reducer is a function that receives the current state and an action object, decides how to update the state if necessary, and returns the new state: (state, action) => newState. 
You can think of a reducer as an event listener which handles events based on the received action (event) type."

이벤트리스너 같은 녀석이라고 하니 쉽게 이해가 되었습니다. state와 action을 이용해서 어떤 new state를 만들지 결정하는 거라고 합니다. 

```
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```
파라미터로 기존 state와 action을 받아오고, 안에서 이런저런 로직으로 새로운 상태를 만든 후 그 새로운 상태를 반환하는 것이 리듀서입니다.

### Store
한 어플리케이션에 하나의 스토어를 두는 게 원칙이라고 합니다. 그렇게 해야 상태관리가 쉬울테니까요. 
스토어 안에는 몇 가지 내장함수가 있는데, 우선 `dispatch`는 상태 안에 있는 함수를 업데이트할 수 있다고 합니다. 
즉, 액션을 만들기만 해서는 변화가 일어나지 않을텐데 그 액션을 trigger해서 변화를 만들어내는 녀석이 dispatch인 것 같습니다. 

```
store.dispatch({ type: 'counter/increment' })
console.log(store.getState())
// {value: 1}
```
getState()는 state에 있는 {value: 1}를 가져오는데, 그게 아니라 어떤 특정한 정보만을 가져오고 싶다면 `selectors`를 이용할 수 있습니다. 
```
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```
이 함수들이 서로 어떻게 작동하는지는 아래 페이지에서 제일 아래 있는 그림이 정말 잘 표현하고 있는 것 같아요. 데이터들이 어떻게 다루어지는지 직관적으로 이해할 수 있게 되었습니다. 
https://redux.js.org/tutorials/essentials/part-1-overview-concepts

## Redux DevTools Extension을 통해 Redux app structure 살펴보기
아래 페이지에 있는 내용을 따라하며 살펴보았습니다. 이거 하기 전에 벨로퍼트님 블로그에 있는 내용도 따라해보았는데 튜토리얼을 먼저 보고 했더라면 이해가 더 빨랐겠다고 생각했어요.
https://redux.js.org/tutorials/essentials/part-2-app-structure

## store 만들기
```
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```
Redux Toolkit에 있는 configureStore를 이용해서 Store를 만들었다고 합니다. 그리고 스토어를 만들 때 `reducer` argument가 있어야 하는데, 
`{counter: counterReducer}`라고 쓰면 그건 'state.counter를 업데이트할 때 counterReducer 함수를 원한다'라는 뜻이라는 것 같습니다.

Redux slice는 각 feature에 대한 리듀서 액션들을 모아놓은 거라고 합니다. 
```
import { configureStore } from '@reduxjs/toolkit'
import usersReducer from '../features/users/usersSlice'
import postsReducer from '../features/posts/postsSlice'
import commentsReducer from '../features/comments/commentsSlice'

export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
```
이렇게 store를 만들 수 있는데, `state.users`slice를 업데이트할 때 usersReducer가 책임지도록, userReducer를 'slice reducer' function으로 쓴다고 하네요.

그럼 아까 다시 카운터 예시로 돌아가서, counter의 slice reducer인 counterReducer를 만들도록 합니다. 
```
import { createSlice } from '@reduxjs/toolkit'
export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions
export default counterSlice.reducer
```

리덕스 툴킷에 있는 createSlice를 사용해서 만드네요. 
저 reducers안에 들어있는 increment, decrement, incrementByAmout는 이 전에 정의된 redux action 타입들인데요.
정확히 말하자면 `{type: "counter/increment"}` 이런 것이겠지만... 저기 name에 있는 counter와 reducer function인 increment 가 합쳐져서 컴퓨터가 이렇게 action type을 똑똑하게 만들어주는 모양입니다.

# 다음 주 목표
일단 여기까지 읽었고요... 튜토리얼 나머지 부분도 열심히 읽고 다음 주에는 직접 해보려고 합니다. 
