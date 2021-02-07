# Redux
- js App(주로 React)에 사용되는 state container
- Action에 반응하여 State를 변경하기 때문에 React와 잘 어울림
- Application의 Global State를 더 쉽게 관리, 공유
- 오직 `하나의 store`만 존재한다.(권장 사항, 하나 이상 존재 가능)
- Component에서 Global State의 특정 값을 의존하게 될 때, 해당 값이 `바뀔 때에만 리렌더링`되도록 최적화가 되어있다.
- 따라서, global state가 변하지 않을 때에는 추가적인 렌더링이 발행하지 않는다.
## Flux 구조
- client-side web application을 구축할 때 사용하는 아키텍처
- MVC 구조의 단점을 보완
- unidirectional data flow 구조
- `Redux는 Flux의 구현체`로 볼 수 있다.

## 기본 개념
### Action
- Application의 store로 data를 보내는 방법
- view에서 action을 호출하면 action creator가 application의 state를 변경한다.
```js
const ADD_TODO = 'ADD_TODO'

function addTodo(text){
    return {
        type: ADD_TODO, //일반적으로 문자열 상수로 정의되는 Action의 Type
        text
    }
}
```
### Reducer
- action의 결과에 따라 `어떻게 Application의 상태가 바뀌는지 특정하게 되는 함수`
- action Type에 따라 변화된 state를 반환하게 된다.

### Store
- Action, reducer를 저장하는 Application에 `단 하나 존재하는 객체`
```js
import {createStore} from 'redux';
import todoApp from './reducers';

let store = creatStore(todoApp);
```

## middleware-saga
- action을 monitoring하고 있다가, 특정 Action이 발생하면 이에 따른 작업을 행하는 방식으로 사용
- thunk에서 못하는 작업들을 처리할 수 있다.    
    - async한 작업을 할 때 기존 요청을 취소
    - 특정 Action 발생시 이에 따라 다른 action이 dispatch되게 하거나, js code 실행
    - Web Socket 사용시 Channel이라는 기능을 통해 더 효과적으로 코드 관리 가능(https://medium.com/@pierremaoui/using-websockets-with-redux-sagas-a2bf26467cab)
    - API 요청 실패시 재요청 작업 수행 가능

### Generator
- 함수 작성시 특정 구간에 멈춰두고 원하는 때에 다시 돌아가게 할 수 있다.
- 결과값을 여러 번 반환할 수도 있다.
- redux-saga에서는 이 `Generator Function을 saga라고 부른다.`
```js
function* test(){ //generator function
    yield 1;
    yield 2;
    yield 3;
}

const generator = test();

gen.next();
//{value: 1, done: false}
gen.next();
//{value: 2, done: false}
gen.next();
//{value: 3, done: false}
gen.next();
//{value: undefiend, done: false}
```
#### Generator로 Action Monitoring
- 아래와 같이 monitoring하며 특정 Action 발생시 code 실행
```js
function* watchGenerator(){
    console.log("monitoring");
    while(true){
        const action = yield;
        if(action.type==='HELLO'){
            console.log("HELLO");
        }
        if(action.type==='BYE'){
            console.log("BYE");
        }
    }
}

const watch = watchGenerator();
watch.next();
//monitoring
watch.next({type: 'HELLO'});
///HELLO
```
https://react.vlpt.us/redux-middleware/11-redux-saga-with-promise.html