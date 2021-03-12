# 4주차 학습

## 리액트 상태관리

>  리덕스, MobX 등을 많이 사용하는 것 같고, 간단한 앱의 경우에는 Context API를 사용하기도 하는 것 같다.

## 리덕스

### 액션

상태에 어떠한 변화가 필요할 때, `액션` 을 발생시킨다.

`액션`은 하나의 객체로 표현된다.

```javascript
{
  type: 'TOGGLE_VALUE'
}
```

`type` 필드를 필수로 가지고 있어야하며, 그 외에는 개발자 마음대로 프로퍼티를 넣을 수 있다.

```javascript
{
	type: 'NAME',
  data: {
    id: 0,
    text: 'junwoo'
  }
}
```

<br>

### 액션 생성함수

`액션` 을 만드는 함수이다.

단순히 파라미터를 받아와서 액션 객체를 만들어주는 역할을 한다.

```javascript
export function addTodo(data) {
  return {
    type: 'ADD_TODO',
    data
  }
}
```

`액션 생성함수` 가 이렇게 따로 존재하는 이유는, 컴포넌트에서 더욱 쉽게 액션을 발생시키기 위함이다.

즉, 필수는 아니다. 직접 액션 객체를 생성해도 된다.

<br>

### 리듀서

변화를 일으키는 함수이다. 리듀서는 두가지 파라미터를 받는다.

```javascript
function reducer(state, action) {
  // 상태 업데이트 로직
  return alteredState;
}
```

리듀서는 현재의 상태와 전달받은 액션을 참고하여 새로운 상태를 만들어 리턴한다.

ex) 카운터 리듀서

```javascript
function counter(state, action) {
  switch(action.type) {
    case 'INCREASE':
      return state + 1;
    case 'DECREASE':
      return state - 1;
    default:
      return state;
  }
}
```

<br>

### 스토어

리덕스를 사용하면, 하나의 애플리케이션에 하나의 스토어를 가진다.

스토어 안에는 현재의 앱 상태와 리듀서, 그리고 `몇가지 내장 함수`들이 들어있다.

<br>

### 디스패치

디스패치는 스토어의 `몇가지 내장 함수` 들 중 하나이다.

디스패치는 `액션을 발생시킨다.` 로 이해하면 된다. dispatch라는 함수에 액션을 파라미터로 전달한다.

`dispatch(action)` -> 스토어가 리듀서 함수 실행 -> 해당 액션을 처리 -> 새로운 상태 반환

<br>

### 구독(Subscribe)

구독도 스토어의 `몇가지 내장 함수` 들 중 하나이다.

`subscribe 함수` 에 특정 함수를 파라미터로 전달해주면, 액션이 디스패치되었을때마다 전달해준 함수가 호출된다.

<br>

## 리덕스의 3가지 규칙

### 1. 하나의 애플리케이션에는 단 하나의 스토어만 존재한다.

물론 여러개의 스토어를 만들 수는 있지만, 권장되지 않는다.

### 2. 상태는 읽기전용이다.

> 리액트에서는 state를 업데이트할 때 setState를 사용하거나, 
> 배열의 경우에는 push하지않고 concat같은 함수로 기존 배열을 수정하지않고 새로운 배열을 만들어 교체하는 방식을 사용하고,
> 객체의 경우에도 기존 객체는 건들지않고, `Object.assign` 이나 spread 연산자를 사용한다.

리덕스도 마찬가지이다. 기존의 상태는 건들지않고, 새로운 상태를 생성하여 업데이트하는 방식을 사용한다.

이를 `불변성을 유지한다.` 라고 표현한다.

리덕스 내부적으로 데이터가 변경되는 것을 감지하기위해 shallow equality 검사를 수행한다.

이를 통해 객체의 변화를 감지할 때, 객체의 깊숙한 내부까지 비교하지 않고 단순히 겉핥기 식으로만 비교해서 좋은 성능을 유지하게된다.

### 3. 리듀서는 순수함수여야한다.

- 리듀서 함수는 이전 상태, 액션 객체를 파라미터로 받는다.
- 이전 상태는 건드리지않고, 변화를 일으킨 새로운 상태 객체를 만들어서 리턴해야한다.
- 똑같은 파라미터로 호출된 리듀서 함수는 항상 똑같은 결과를 리턴해야한다.

즉, `new Date()` 와 같은 애드을 사용하는 경우 리듀서 함수의 바깥에서 처리해주어야한다.



---

>  커리큘럼을 따라, https://react.vlpt.us/redux/05-counter.html 예제를 구현해보았습니다.
>
> 단순한 카운터 앱이라 그런지, 막연히 생각했던 복잡하고 러닝커브높은 리덕스의 이미지와는 달리 간결한 모습을 보였습니다. 해보지도 않고 오해를 많이 가지고 있었던 것 같습니다.



## Recoil

검색하다보니 mobX 말고도 Recoil이라는 상태관리 라이브러리가 있어서 한 번 찾아보았습니다.

Recoil API는 단순하다. 훅스를 사용하고 있다면 익숙할 거라고 한다.

Recoil을 시작하기위해서는 애플리케이션을 `RecoilRoot` 로 감싸고, 데이터를 `atom` 이라는 단위로 선언하여 `useState` 를 Recoil의 `useRecoilState` 로 대체해야 한다.



### 학습

#### - Atom

atom은 하나의 상태로 이해하면 된다. atom의 값을 변경하면 그것을 구독하고 있는 모든 컴포넌트들이 리렌더링된다.

atom을 생성하기위해서는 애플리케이션의 고유한 키 값과 디폴트 값을 설정해야한다.

```
export const nameState = atom({
	key: 'nameState',
	default: 'junwoo'
})
```

#### - useRecoilState

atom값을 구독하여 업데이트할 수 있는 hook이다.

`useState` 와 동일한 방식이다.

<br>

#### - useRecoilValue

atom 값을 리턴한다.

<br>

#### - useSetRecoilState

setter함수를 반환한다.



사용법을 보면.... 굉장히 직관적이다.

```
import { nameState } from './example'

// useRecoilState
const NameInput = () => {
	const [name, setName] = useRecoilState(nameState);
	const onChange = event => {
		setName(event.target.value);
	}
	
	return (
		<>
			<input value={name} onChange={onChange} />
			<div>name : {name}</div>
		</>
	)
}


// useRecoilValue
const NameComponent = () => {
	const name = useRecoilValue(nameState);
	
	return <div>{name}</div>
}

// useSetRecoilState
const otherNameComponent = () => {
	const setName = useSetRecoilState(nameState);
	return <button onClick={() => setName('junwoo')}>BTN</button>
}
```







차주나 차차주에는... 
간단한 투두앱을 Redux, MobX, Recoil을 사용하여 만들어보는 식으로 학습을 이어나가도 좋을것 같아서 진행하려고 한다.



## 레퍼런스

https://react.vlpt.us/redux/02-rules.html

https://ui.toast.com/weekly-pick/ko_20200616

