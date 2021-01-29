# React
## 설정
### 절대경로로 Import
```json
tsconfig.json 파일에
{
    "compilerOptions": {
        ...,
        "baseUrl": "./src" 추가
    }
}
```
```typescript
import Component from "../components/Component"; //대신
import Component from "components/Component"; //로 import 가능
```

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
#### useLayoutEffect
- useEffect와 동일하나, `DOM 변경 후에 동기적으로 발생`
- DOM에서 Layout을 읽고 동기적으로 Re-rendering하는 경우 사용

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
- 이를 통해 전달된 함수는 `렌더링 중에 실행`된다.
```typescript
const memoizedValue = useMemo(() => computeValue(a,b), [a,b]); //없는 경우 렌더링 마다 새 값을 계산
```
```js
//예시
// List가 일정하다고 가정하면, searchText가 바뀌었을 때에만 다시 해당 function을 호출한다.
// 이 경우엔 searchText가 바뀌면 아래 셋 다 호출이 된다.
const searchedList = useMemo(()=>{
  return list.filter(item => item.value.include(searchText));
}, [list, searchText]);

//searchText가 바뀌거나, filter의 내용이 바뀌는 경우에 호출 된다.
//filter의 내용이 바뀐 경우에는 아래 function과 pagingList만 호출이 된다.
//만약 위 searchedList에서 useMemo를 사용하지 않았을 경우, searchedList는 변경이 필요가 없음에도 변경이 일어난다.
const sortedList = useMemo(()=>{
  return searchedList.sort((c, n) =>{
    return c.value - n.value;
  });
}, [searchedList, filter]);

//얘는 무슨 일이 있어도 호출이 되므로 굳이 useMemo()를 사용할 필요는 없어 보인다.
const pagingList = useMemo(()=>{
  const offset = (page - 1) * 10;
  return sortedList.slice(offset).slice(0,10);
}, [sortedList, page]);
```

### useRef
- Class Component에서 사용하는 ContextAPI를 Functional Component에서 사용할 수 있게 하는 훅
#### Context
- Context를 사용하여 단계마다 일일이 Props를 넘겨주지 않고, Component Tree 전체에 데이터 제공 가능(Redux?)
- 즉, Global하게 Data(State)를 공유
```js
class App extends React.Component {
  render() {
    return (<Toolbar theme="dark" />);
  }
}

function Toolbar(props){
  return (<div> 
      <ThemedButton theme={props.theme} />  {/* theme props를 명시적으로 넘겨주고 있다. */}
    </div>);
}

class ThemedButton extends React.Component {
  render() {
    return <Button theme={this.props.theme} />;
  }
}

///아래는 Context 사용
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

function Toolbar(){
  return (
    <div>
      <ThemedButton /> {/* 여기서는 테마를 넘기지 않는다.*/}
    </div>
  );
}

class ThemedButton extends React.Component {
  static contextType = ThemeContext;
  render(){  {/* Context를 사용하여 렌더링*/}
    return <Button theme={this.context} />;
  }
}
```
##### 주의점
- Context를 자주 사용하면 컴포넌트의 재사용이 어려워진다.
- 여러 level에 걸쳐 props를 넘길 때에는 `Component Composition이 더 효과적`일 수 있다.
#### Component Composition
- Inheritance 대신 Composition을 사용하여 Component 간 코드를 재사용하는 것이 좋다.
##### Component에 다른 Component 담기
- 어떤 자식 element가 들어올 지 예상할 수 없는 경우, 특수한 children prop를 사용하여 Children element를 그대로 전달하는 것이 좋다.
```js
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
//이런 방식으로 다른 Component에서 JSX를 중첩하여 임의의 Children을 전달할 수 있다.
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1> {/* FancyBorder 안에 있는 것들이 children props로 전달되어 출력.*/}
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```
##### 특수화
- Composition을 통해 `구체적인 Component`가 `일반적인 Component`를 렌더링하고 props를 통해 내용 구성
- props로 동적으로 값을 전달하는 것을 일컫는 듯
```js
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```
#### 결론
- Redux + slice + saga 구조에서 사용할 일이 있을지 모르겠는 Hook
- 그래서 아직까지는 사용해 본 적이 없다.
## List & Key
### Key
- List로 삽입된 Component에서 key는 React가 어떤 항목을 변경, 추가, 제거할 지 식별하는 것을 도움
- Element의 안정적 고유성을 부여하기 위해 습관적으로 삽입해야 함
- 대부분의 경우 Data의 ID(문자열)를 key로 사용한다.
- 항목의 순서가 바뀔 수 있는 경우(sort(?)) key에 Index를 사용하면 안됨! -> 성능 저하를 야기할 수 있음
- 명시적으로 key를 지정하지 않으면 `default Key = index`
- Key는 배열 안에서 `형제 사이에 고유`해야 하며, `전체 범위에서 고유할 필요는 없다.`

## React.memo
- UI 성능 증가를 위해 React.memo()를 통해 렌더링 결과를 memoization
- 이를 통해 불필요한 Re-rendering을 건너뛸 수 있다.
- Functional Component가 같은 props로 자주 렌더링될 것이라 예상될 때 사용한다.
- 성능 관련 변경이 잘못 적용된다면, 성능이 오히려 악화될 수 있다.
- 얕은 비교를 하므로 비교 방식을 수정하고 싶다면 아래와 같이
```typescript
React.memo(Component, [areEqual(prevProps, nextProps)]);
```
### 동작
1. React.memo()로 Component가 Wrapping되면 렌더링 후 결과를 `Memoizing`
1. 이후 다음 Rendering시 `props가 같다면` Memoization된 내용을 재아용

## React.FC
- props의 Type을 Generic으로 넣어서 사용한다.
### 장점
- props에 기본적으로 children이 들어간다.
- defaultProps, propsType, contextTypes를 설정할 때 자동완성이 될 수 있다.
### 단점
- children이 Optional 형태로 들어가서 props의 Type이 명백하지 않다.(children의 유무)
- 어떤 Component는 Children이 필수, 어떤 Component는 반드시 없어야 할 수 있다.
- defaultProps가 제대로 작동하지 않는다. => 이게 좀 크리티컬해 보임
### 결론
- 아직은 쓰지 말자

### 사용하지 말아야할 때
- 쓸모없는 props 비교 -> 대부분 false를 반환하는 비교 함수
### React.memo with callback function
- Function Object는 일반 Object와 동일한 비교 원칙을 따른다. (= 자기 자신에게만 동일하다.)
- Parent Component가 Child Component의 Callback Function을 정의한다면 새 Function이 생성될 수 있음 -> Memoization이 될까?
- re-render될 때 다른 instance를 넘기게 된다면 memoization이 발생하지 않을 것이다.
- useCallback()을 사용해서 Callback Instance를 보존할 수 있다.