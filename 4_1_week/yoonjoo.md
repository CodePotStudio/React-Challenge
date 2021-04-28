# 이번 주에 한 일
지난 주까지는 리덕스 튜토리얼을 읽으면서 상태관리를 어떻게 하는지 이해하려고 노력했는데, 아무래도 인강으로 하나 보면 좋을 것 같아서 노마드코더 강의를 보았습니다.
그런데 리덕스 강의를 듣기 전에 리액트 훅에 관한 강의를 먼저 보고 오라고 해서, 리덕스를 이해하려던 노력을 잠시 멈추고 리액트 훅에 대한 강의를 듣기 시작했습니다. 
와 그런데 벌써 신세계인 것 같고 눈이 되게 편하네요. 이래서 그동안 챌린지하면서 많은 분들이 리액트 훅에 대해서 리뷰하셨던 거구나,하고 깨달았습니다. 이번 주에 강의 들은 걸 정리해보겠습니다.

## 리액트 훅에 대한 노마드 코더 강의
https://nomadcoders.co/react-hooks-introduction/lobby

## useState
증가 버튼 누르면 숫자 올라가고, 감소 버튼 누르면 숫자 줄어드는 그 카운터인데요, 이렇게 짧고 간단하게 함수형으로 쓸 수 있다는 것을 처음 알게 되었습니다. 
```
import React, { useState } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

const App = () => {
  const [item, setItem] = useState(1);
  const incrementItem = () => setItem(item + 1);
  const decrementItem = () => setItem(item - 1);
  return (
    <div className="App">
      <h1>Hello {item}</h1>
      <h2>Start editing to see some magic happen!</h2>
      <button onClick={incrementItem}>Increment</button>
      <button onClick={decrementItem}>Decrement</button>
    </div>
  );
};


const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
저는 사실 그동안 리액트 챌린지를 하면서 점점 리액트가 재밌다고 생각은 했지만 일일이 setState 쓰고, this가 엄청 많이 나오고 하는 게 되게 번거롭다고 생각했거든요. 
그렇게 리액트가 그렇게 세련되었다고 하는데 왜 나는 왜 이게 자바보다 더 군더더기가 많은 거 같지,하는 생각을 하고 있었는데 (물론 공부를 꾸준히 안하고 드문드문하니까 실력이 안느는 자의 변명입니다) 
이번 주에 배운 내용이 사실 저에겐 제일 재밌네요.

```
const [item, setItem] = useState(1);
```
useState함수는 저렇게 배열을 리턴하게 되는데, state변수와 그 변수를 바꾸는 함수를 리턴합니다. 그리고 state변수의 초기값을 저렇게 설정할 수 있고요. 

```
const incrementItem = () => setItem(item + 1);
const decrementItem = () => setItem(item - 1);
```
그리고 앞으로 버튼을 눌렀을 때 state값을 바꾸는 함수들을 작성하는데, 이 때 저 setItem을 이용해 state를 어떻게 바꿀 것인지 정의합니다.
```
 <button onClick={incrementItem}>Increment</button>
 <button onClick={decrementItem}>Decrement</button>
 ```
 그러고 나서 저렇게 버튼 onClick에다가 함수들 넣어주면 끝이네요! 
 
 ## Hook 만들어보기 - useInput
 ```
...생략...

const useInput = (initialValue, validator) => {
  const [value, setValue] = useState(initialValue);
  const onChange = (event) => {
    const {target: {value}} = event;    
    let willUpdate = true;
    if(typeof validator ==="function"){
      willUpdate = validator(value);
    }
    if(willUpdate){
      setValue(value);
    }
  }  
  return {value, onChange};
};

const App = () => {
  const maxLen = value => value.length < 10;
  const name = useInput("Name? ", maxLen);
  return (
    <div className="App">
      <h1>Hello </h1>
      <input placeholder="Name" {...name}/>
    </div>
  );
};
...생략...
```
이번에는 직접 훅을 만들어서 써보는 걸 해봤는데요, 입력창에 이름을 쓸 때 입력한 내용을 검사해서 제한하는 걸 만들었습니다.
```
const maxLen = value => value.length < 10;
const name = useInput("Ms. ", maxLen);
```
우선, 이름 입력창에서 이름의 길이를 제한하는 maxLen을 만들고, 앞으로 만들 useInput의 파라미터로 초기값인 "Name?"이랑 maxLen을 넣어줬어요.
그러면 이 useInput이 뭔가를 리턴할거고, 그 리턴받은 값들을 이용해 화면에 띄우게 되겠죠. 
```
const useInput = (initialValue, validator) => { 
  ...
}
```
이게 앞으로 작성할 useInput인데, initialValue를 받아다가 useState를 이용해서 초기값 설정을 할 거고, validator를 이용해서 입력값을 검증한 후 state를 바꿀지말지를 결정하도록 합니다.
```
const [value, setValue] = useState(initialValue);
```
맨 처음에 배웠던 useState를 이용해서 value라는 상태변수를 initialValue로 세팅하고, 그 value를 바꿀 수 있는 함수인 setValue 역시 리턴받습니다.
```
 const onChange = (event) => {
    const {target: {value}} = event;    
    let willUpdate = true;
    if(typeof validator ==="function"){
      willUpdate = validator(value);
    }
    if(willUpdate){
      setValue(value);
    }
  }  
```
이벤트가 발생했을 때, 즉, 입력폼에 뭘 썼을 때 실행되는 함수 onChange라는 거까지는 이해했는데 사실 
```
const {target: {value}} = event;  
```
이 부분이 이해가 잘 안가네요. 특히 저 target: {value}에서 왜 value만 쓰지 않고 괄호와 함께 쓰는 건지 궁금합니다. 
```
 let willUpdate = true;
 if(typeof validator ==="function"){
      willUpdate = validator(value);
    }
 if(willUpdate){
      setValue(value);
 }
```
어쨌든 불리언을 리턴할 validator에다가 value를 집어넣어서 그게 참인지 거짓인지 알아내고, 참이면 setValue를 통해 value의 값을 바꾸는 역할을 하게 됩니다. 
이렇게해서 이름 입력창에 글자수를 제한한다거나, 특수문자 제한을 한다거나 다양하게 이용할 수 있겠네요. 
`return {value, onChange};`을 useInput이라는 훅이 리턴을 하고, 그걸 앱에서는 
```
const App = () => {
  const maxLen = value => value.length < 10;
  const name = useInput("Name? ", maxLen);
  return (
    <div className="App">
      <h1>Hello </h1>
      <input placeholder="Name" {...name}/>
    </div>
  );
};
```
요렇게 써먹네요. 특히 저는 저 `{...name}`라고 쓰는 게 인상깊었는데, 일일이 name.value 이런 식으로 안써도 되고 간단히 저렇게 쓸 수 있다는 게 좋다고 생각했습니다. 
