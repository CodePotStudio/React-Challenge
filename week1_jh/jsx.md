# JSX
- 내장형 XML같은 구문
- TS에서는 embedding, type check, js로의 직접 컴파일을 지원
- 파일 이름을 .tsx로 지정, jsx 옵션을 활성화해야 사용 가능
## /** @jsx jsx */
- jsx transformer라고 알리는 custom pragma
- @emotion/core의 css prop를 활성화하기 위한 boiler plate
- babel 설정으로 이를 제거할 수 있지만, CRA(Create-React-App)에서는 babel 커스텀 설정을 막아둠
- react-app-rewired를 사용해야 설정 변경 가능(@emotion/babel-preset-css-prop 설치)
## JSX Mode
- Typescript는 3가지 JSX모드와 함께 제공
- 이 모드들은 방출 단계에서만 영향을 미치며, 타입 검사에는 영향을 받지 않는다.
### preserve
- 다른 변환 단계에 사용하도록 결과의 일부를 유지
- 결과물이 .jsx의 확장자를 가짐
### react
- React.createElement를 생성하여 사용 전에 JSX 변환이 필요하지 않음
- 결과물이 .js의 확장자를 가짐
### react-native
- JSX를 유지한다는 점이 preserve와 동일
- 결과물이 .js의 확장자를 가짐

| Mode | Input | Result | extension |
|------|:-----:|-------:|-----------|
| preserve | <div />| <div /> | .jsx|
| react | <div /> | React.createElement("div") | .js|
| react-native | <div /> | <div /> | .js|

## Type Checking
- 리액트에서 Intrinsic elements는 React.createElement("div")와 같이 생성된다.
- User Custom Component는 React.createElement("MyComponent")가 아니다.
- JSX 요소에 전달되는 속성의 Type은 다르게 조회되어야 한다.(Intrinsic element는 내재적으로 알고 있어야 하지만, 컴포넌트는 자신의 속성 집합을 지정하려 함) -> 이해를 못했음

### Intrinsic elements(내장 요소)
- 특수 인터페이스 JSX.IntrinsicElements에서 조회됨
- 이 인터페이스가 지정되지 않으면 그대로 진행되어 내장 요소 타입은 검사되지 않음
- 이 인터페이스가 있는 경우, 내장 요소의 이름은 JSX.IntrinsicElements 인터페이스의 프로퍼티로 조회됨
```typescript
declare namespace JSX {
    interface IntrinsicElements {
        foo: any
    }
}

<foo />; //성공 
<bar />; //오류 JSX.IntrinsicElements에 지정되지 않음
// catch-all 문자열 인덱서를 지정할 수도 있다.
declare namespace JSX {
    interface IntrinsicElements{
        [elemName: string]: any;
    }
}
```

### Value-based Elements
- 해당 스코프에 있는 식별자로 간단히 조회됨.
- Functional Component, Class Component가 존재
```typescript
import MyComponent from "component/MyComponent";

<MyComponent />;
```

#### Functional Component
- 첫 번째 parameter가 props인 Javascript 함수로 정의됨
- TS는 Component의 Return type이 JSX.Element에 할당 가능하도록 요구
- 함수 오버로딩 가능
```typescript
interface FooProp{
    name: string;
    X: number;
    Y: number;
}

declare function Component(props: {name: string});
function ComponentFoo(prop: FooProp){
    return <Component name={props.name} />;
}

const Button = (props: {value: string}, context: {color: string}) => <button>
```