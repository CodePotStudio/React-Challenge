# Redux-Saga 이해하기

리액트에서 프로젝트를 하다보면 하위 컴포넌트에 props를 넘겨주는게 너무 힘들어서 상태관리 라이브러리인 Redux를 찾게된다 

그렇게 Redux를 행복하게 쓰고 있다 보면 곧 비동기처리를 해야할 때가 생기는데 그럴 때 마주하게 되는 것이

Reudx-thunk vs Redux-saga

많은 사람들이 Redux-thunk가 주는 깔끔하지 못함(어떤 aciton은 일반 객체를, 어떤 action은 또 다른 dispatch를...)에 만족하지 못해서 Redux-saga를 택해서 프로젝트를 진행하는 것을 볼 수 있었다

saga를 이해하기 전에 제너레이터에 대한 지식이 필요하기 때문에 간단하게 알아보자

# 제너레이터

함수는 일반적으로 하나의 값만 반환한다

하지만 제너레이터 함수는 여러 값을 하나씩 반환할 수 있다

## 제너레이터 함수

제너레이터 함수를 생성하기 위해선 function뒤에 *을 붙여주면 된다

```jsx
function* generateSequence(){
		yield 1;
		yield 2;
		return 3;
}
```

이렇게 생성된 제너레이터 함수는 일반 함수와 다르게 작동하는데

제너레이터 함수를 실행하면 함수 내부의 코드가 실행되지않는 대신 코드를 처리할 수 있는 제너레이터 객체가 반환된다

```jsx
function* generateSequence(){
        yield 1;
        yield 2;
        return 3;
    }
console.log(generateSequence()); // not 3 but [object Generator]
```


<img width="702" alt="generator" src="https://user-images.githubusercontent.com/45571631/110235083-812a5800-7f71-11eb-9b4c-0016c692fc45.png">

    _반환된 제너레이터 객체_


반환된 제너레이터 객체는 다음과 같은 메서드를 갖는다

- next();
- return();
- throw();

## next

next는 제너레이터의 주요 메서드로 next()를 호출하면 제일 가까운 `yield value;` 구문까지 실행된 후 멈춘다

위 코드에선 `yield 1;` 까지 실행한 후 멈추게 되는데 이때 제너레이터 함수는 다음과 같은 객체를 반환한다

```jsx
function* generateSequence(){
      yield 1;
      yield 2;
      return 3;
}
const generator = generateSequence();

const one = generator.next();

console.log(one); // {value: 1, done: false}
```

next가 한 번 호출된 상태에서 generator는 두 번째 줄에 멈춰있는 상태가 된다

```jsx
function* generateSequence(){
      yield 1; // <--------여기에 멈춰있음
      yield 2; 
      return 3;
}
const generator = generateSequence();

const one = generator.next();

console.log(one); // {value: 1, done: false}
```

한 번 더 next를 호출해보자

```jsx
function* generateSequence(){
      yield 1; 
      yield 2; // <--------여기에 멈춰있음
      return 3;
}
const generator = generateSequence();

const one = generator.next();

console.log(one); // {value: 1, done: false}

const two = generator.next();

console.log(two); // {value: 2, done: false}
```

한 번 더..

```jsx
function* generateSequence(){
      yield 1; 
      yield 2; 
      return 3; // <--------여기에서 멈춤
}
const generator = generateSequence();

const one = generator.next();

console.log(one); // {value: 1, done: false}

const two = generator.next();

console.log(two); // {value: 2, done: false}

const three = generator.next();

console.log(three); // {value: 3, done: true}
```

done의 값이 true로 바뀌었다 제너레이터 객체가 끝났다는 뜻인데

이제 아무리 `generator.next();`를 호출해도 제너레이터가 종료됐기 때문에 

{value: undefine, done: true}만 반환한다

### yield의 쌍방 교류

위에선 yield를 이용해 반환값을 넣기만 했지만 반대로 값을 객체 내부로 전달해 줄 수도 있다

```jsx
function* generateBidirection() {
    const value = yield 'put value'; // <---- one

    yield value;  // <---- two
    
    return 'end'; // <---- three
}

const generator = generateBidirection();

const one = generator.next();
console.log(one); // {value: 'put value', done: false}

const two = generator.next(100);
console.log(two); // {value: '100', done: false}

const three = generator.next();
console.log(three); // {value: 'end', done: true}
```

### 제너레이터 내부의 무한 반복

제너레이터 내부에 while문을 써서 무한 끝나지 않는 제너레이터를 만들 수 있는데

이를 이용해서 들어오는 변수를 모니터링하고 변수에 따라서 적절한 작업을 할 수 있는 함수를 만들 수 있다

```jsx
function* generatorWhile(){
    console.log('start');
    while (true){
        const action = yield;
        if(action==='A') console.log('A');
        if(action==='B') console.log('B');
    }
}

const generator = generatorWhile();

generator.next(); // 'start'
generator.next('A'); // 'A'
generator.next('B'); // 'B'
generator.next('A'); // 'A'
```

이런 패턴은 리덕스 사가의 takeEvery를 이해하는데 도움이 된다

# Redux-saga

init 코드 : [https://github.com/redux-saga/redux-saga-beginner-tutorial](https://github.com/redux-saga/redux-saga-beginner-tutorial)

## saga 만들기

Redux-saga에서는 제너레이터 함수를 saga라고 부른다

사가들이 들어갈 파일을 하나 만들어서 테스트용 사가를 생성하자

```jsx
// in sagas.js

export function* helloSaga(){
    console.log('Hello Sagas!');
}
```

## store에 미들웨어 추가하기

```jsx
// in main.js

const sagaMiddleware = createSagaMiddleware(); // 사가 미들웨어를 만들어서

const store = createStore(reducer,applyMiddleware(sagaMiddleware)); // 스토어에 적용시켰다

sagaMiddleware.run(helloSaga)); // 사가 미들웨어 실행
```

다른 미들웨어는 적용만 시켜주면 됐는데 사가미들웨어는 직접 실행 시켜주어야한다는 점이 달랐다

적용된 미들웨어는 로그에 메세지만 남기고 종료된다

## 비동기 호출하기

비동기 호출을 다루기 위해서 Counter에 클릭하면 1초 후에 값이 증가되는 버튼을 만든다

```jsx
// in Counter.js

const Counter = ({ value, onIncrement, onDecrement, onIncrementAsync}) =>
      <div>
          <button onClick={onIncrementAsync}>
              Increment after 1 second
          </button>
          {' '}
			...
}
```

UI에 함수를 적용할 버튼을 생성했으니 main에서 실행할 함수도 넘겨주자

```jsx
// main.js

const action = type => store.dispatch({type})

function render() {
  ReactDOM.render(
    <Counter
      value={store.getState()}
      onIncrement={()=> action('INCREMENT')}
      onDecrement={() => action('DECREMENT')}
      onIncrementAsync={()=> action('INCREMENT_ASYNC')}
    />,
    document.getElementById('root')
  )
}
```

`const action = type => store.dispatch({type})`

이 프로젝트에서는 store에서 접근한 dispatch를 가지고 문자열값을 받아 액션값으로 디스패치하는 action함수를 만들어서 사용한다

이제 준비가 끝났으니 sagas.js에서 비동기 호출을 구현하면 된다

```jsx
// in sagas.js
export function* helloSaga(){
    console.log('Hello Sagas!');
}

export function* incrementAsync() {
    yield delay(1000);
    yield put({type: 'INCREMENT'});
}

export function* watchIncrement(){
    takeEvery('INCREMENT_ASYNC',incrementAsync);
}
```

리덕스 사가의 내장 함수들이 사용되었는데 간단하게 다음과 같다

- delay(number) :  변수의 ms만큼 기다린 만큼 정지한다
- put(action) : action을 디스패치시킨다
- takeEvery(action, saga) : action을 계속해서 모니터링하다가 해당 action이 디스패치되면 saga를 실행시킨다

위의 함수는

1. watchIncrement의 takeEvery가 'INCREMENT_ASYNC' 액션을 계속 모니터링한다
2. 'INCREMENT_ASYNC' 액션이 발생하면 incrementAsync를 실행시킨다
3. incrementAsync는 `yield delay(1000)`실행 후 1초간 대기
4. 1초 대기가 끝나면 `yield put({type: 'INCREMENT'})` 로 INCREMENT 액션을 디스패치한다

이제 적용해야할 사가가 helloSaga와 watchIncrement 로 두 개로 늘어났는데

comineReducers처럼 이들을 합쳐서 실행시켜야한다

```jsx
// in sagas.js
...
function* incrementAsync() {
    yield delay(1000);
    yield put({type: 'INCREMENT'});
}

function* watchIncrement(){
    takeEvery('INCREMENT_ASYNC',incrementAsync);
}

export function* rootSaga(){
    yield all([
        helloSaga,
        watchIncrement,
    ]);
}
```