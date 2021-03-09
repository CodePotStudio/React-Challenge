# 리액트의 기원
 - 기존 뷰를 날려버리고 처음부터 새로 렌더링하는 방식
 - 이렇게 하면 애플리케이션 구조가 매우 간단하고, 작성해야 할 코드양도 많이 줄어든다.

# 리액트 정의
 - 자바스크립트 라이브러리로 사용자 인터페이스를 만드는데 사용합니다.
 - 구조가 MVC, MVW 등인 프레임워크와 달리 오직 V(View)만 신경씀
 - 컴포넌트 : 특정 부분이 어떻게 생길지 정하는 선언체입니다. 재사용이 가능한 API로 수많은 기능을 내장합니다.
 - 렌더링 : 사용자 화면에 뷰를 보여주는 것입니다.
 - render() : 컴포넌트가 어떻게 생겼는지 정의하는 역할을 합니다.

# 조화 과정(Reconciliation)
 - "조화 과정을 거친다"라고 하는 것이 더 정확한 표현
 - 컴포넌트는 데이터를 업데이트했을 때 단순히 업데이트한 값을 수정하는 것이 아니라, 새로운 데이터를 가지고 render 함수를 다시 호출합니다.
 - 이 때 render 함수가 반환하는 결과를 곧바로 DOM에 반영하지 않고, 이전에 render 함수가 만들었던 컴포넌트 정보와 현재 render 함수가 만든 컴포넌트 정보를 비교합니다.
 - 둘의 차이를 알아내 최소한의 연산으로 DOM 트리를 업데이트한다.


# Virtual DOM
 - DOM : Document Object Model의 약어 객체로 문서 구조를 표현하는 방법 XML이나 HTML로 작성합니다.
 - DOM은 트리 형태라서 특정 노드를 찾거나 수정/제거하거나 원하는 곳에 삽입할 수 있습니다.
 - DOM은 동적 UI에 최적화되어 있지 않다는 것입니다.
 - Virtual DOM을 사용한다고 해서 사용하지 않을 때와 비교하여 무조건 빠른 것은 아닙니다.
 - 적절한 곳에 사용해야 리액트가 지닌 진가를 비로소 발휘
 - 작업이 매우 간단할 때 사용하면 좋지 않습니다.(단순 라우팅 정도만 있는 정적 페이지)

# JSX의 문법
 - 감싸인 요소
 - 컴포넌트에 여러 요소가 있다면 반드시 부모 요소 하나로 감싸야 합니다.
 - 컴포넌트를 선언하는 방식 2가지
   1. 함수형 컴포넌트
       - 메모리 자원을 덜 사용합니다.
       - 클래스형 컴포넌트보다 선언하기 훨씬 편합니다.
       - 빌드 후 배포할 때도 결과물의 파일 크기가 더 작습니다. 
   2. 클래스형 컴포넌트
       - state 기능 및 라이프사이클 기능을 사용할 수 있습니다.
       - 임의의 메서드를 정의할 수 있습니다.
# Props
 - properties를 줄인 표현
 - props 값은 해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서 설정할 수 있습니다.
 - props 값은 컴포넌트 함수의 파라미터로 받아와서 사용할 수 있습니다. JSX 내부에서 {} 기호로 감싸주면 됩니다.
 - defaultProps 
   - props의 기본값을 설정할 때 사용합니다.
   
# state 
 - state는 컴포넌트 내부에서 바뀔 수 있는 값을 의미합니다.
 - 클래스형 컴포넌트가 지니고 있는 state와 함수형 컴포넌트에서 useState라는 함수를 통해서 사용하는 state가 있습니다.
  
# key
 - 컴포넌트 배열을 렌더링했을 때 어떤 원소에 변동이 있었는지 알아내려고 사용합니다.
 - 예를 들어 유동적인 데이터를 다룰 떄 원소를 새로 생성할 수도, 제거할 수도, 수정할 수도 있습니다.

# 라이프사이클 메서드
 - 마운트, 업데이트, 언마운트로 나뉩니다.
 - will 접두사가 붙은 메서드는 어떤 작업을 작동하기 전에 실행하는 메서드
 - Did 접두사가 붙은 메서드는 어떤 작업을 작동한 후에 실행되는 메서드
 - 마운트 
    - DOM이 생성되고 웹 브라우저상에 나타나는 것
    - constructor : 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드
    - getDerivedStateFromProps : props에 있는 값을 state에 넣을 때 사용하는 메서드
    - render : 우리가 준비한 UI를 렌더링하는 메서드
    - componentDidMount : 컴포넌트가 웹 브라우저 상에 나타난 후 호출되는 메서드
 
 - 업데이트
    - props가 바뀔 때
    - state가 바뀔 때
    - 부모 컴포넌트가 리렌더링될 떄 
    - this.forceUpdate로 강제로 렌더링을 트리거할 때  
    - getDerivedStateFromProps : 마운팅 과정에서도 호출되며, 업데이트가 시작하기 전에 호출됩니다. props의 변화에 따라 state 값에도 변화를 주고 싶을 때 사용
    - shouldComponentUpdate : 컴포넌트가 리렌더링을 해야 할지 말아야 할지 결정하는 메서드, true를 반환하면 다음 라이프사이클 메서드를 계속 실행
    - render : 컴포넌트를 리렌더링
    - getSnapshotBeforeUpdate : 컴포넌트 변화를 DOM에 반영하기 직전에 호출하는 메서드
    - componentDidUpdate : 컴포넌트의 업데이트 작업이 끝난 후 호출하는 메서드

 - 언마운트 : 마운트의 반대 과정, 즉 컴포넌트를 DOM에서 제거하는 것
    - componentWillUnmount : 컴포넌트가 웹 브라우저 상에서 사라지기 전에 호출하는 메서드

# Hooks
 - 리액트 v16.8에 새로 도입된 기능 
 - 함수형 컴포넌트에서도 상태 관리를 할 수 있는 useState, 렌더링 직후 작업을 설정하는 useEffect 등의 기능을 제공합니다.
 - useState
   - 함수형 컴포넌트에서도 가변적인 상태를 지닐 수 있게 해줍니다.   
 - useEffect
   - 리액트 컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정할 수 있는 Hooks
   - 클래스형 컴포넌트의 componentDidMount와 componentDidUpdate를 합친 형태로 보아도 무방하다.
   - 마운트될 때만 실행하고 싶을 때
     - 함수의 두 번재 파라미터로 비어 있는 배열을 넣어주면 된다.
      
```javascript
useEffect(() => {
	console.log("마운트될 때만 실행");
}, []);
```

  - 특정 값이 업데이트될 때만 실행하고 싶을 때
    - 두 번째 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어주면 된다.
```javascript
useEffect(() => {
	console.log(name);
}, [name]);
```
  
  - 뒷 정리하기
    - 컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷정리(cleanup)함수를 반환해줘야한다.

```javascript
useEffect(() => {
	console.log('effect');
	console.log(name);
	return () => {
		console.log('cleanup');
		console.log(name);
	};
});
```

# useReducer
   - useState보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트 해주고 싶을 때 사용하는 Hook이다.
   - reducer 함수는 현재 상태, 업데이트를 위해 필요한 정보를 담은 액션(action) 값을 전달받아 새로운 상태를 반환하는 함수이다.
   - 리듀서 함수에서 새로운 상태를 만들 때는 반드시 불변성을 지켜주어야 한다.

```javascript
const reducer(state, action) => {
	switch(action.type) {
		case 'INCREMENT':
			return {value: state.value + 1};
		case 'DECREMENT':
			return {value: state.value + 1};
		default:
			return state;
	}
}

...

const [state, dispatch] = useReducer(reducer, {value: 0});
```

  - useReducer의 첫 번째 파라미터에는 리듀서 함수를 넣고, 두 번째 파라미터에는 해당 리듀서의 기본 값을 넣어준다.
  - 이 Hook을 사용하면 state 값과 dispatch 함수를 받아오는데, state는 현재 가리키고 있는 상태이고, dispatch는 액션을 발생시키는 함수이다.
  - dispatch(action)과 같은 형태로, 함수 안에 파라미터로 액션 값을 넣어주면 리듀서 함수가 호출되는 구조이다.
  - useReducer를 사용했을 때의 **가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 바깥으로 빼낼 수 있다는 것**이다.
