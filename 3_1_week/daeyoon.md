## react의 여러 코드 대해 살펴보기-01
오늘은 드디어 지난 리뷰에서는 react를 사용하는 이유에 대해서 알아보았다면 본격적으로 이번 리뷰에서 부터 리엑트의 코드에 대한 리뷰를 시작해 보겠습니다!
그래서 상태, 속성, 함수, 가상돔과 중요한 훅에 대한 기초를 익히도록 하겠습니다!

### 1. Props, State
react 컴포넌트에서는 UI에 관련한 데이틑 prop과 state로 관리해야합니다. 만약 이처럼 외부에 선언을 하여 사용한다면 클릭을 해도 변경되지 않습니다. 클릭을 했을 때 color는 변경이 이루어 졌지만 react가 반응을 하지 않습니다.
```
let color = 'red'
function App() {
  const isRed = (() => color === 'red')();
  const onClick = () => {
    color = isRed ? 'blue' : 'red';
  }
  return (
    <button style={{ backgroundColor: color }} onClick={ onClick }>
        색토글
    </butoon>
  );
}
export default App;
```

하지만 이렇게 useState를 통해서 선언하게 된다면 react가 반응하여 변경한 값에 맞게 변경됩니다.

```
function App() {
  const [ color, setColor ] = useState('red');
  const isRed = (() => color === 'red')();
  const onClick = () => {
    setColor( isRed ? 'blue': 'red');
  }
  return (
    <button style={{ backgroundColor: color }} onClick={ onClick }>
        색토글
    </button>
  );
}
export default App;
```
prop을 통해서 진행할 수도 있습니다. prop로 전달받아서 한다면 마찬가지로 color의 변화에 따라 잘 변화하지만 기본적으로 useState를 통해서 선언되었기 떄문에 되는것이 당연합니다.
```
function ToggleColorButton({ color }) {
  return (
    <button style={{ backgroundColor: color }}>
        색토글
    </button>
  );
}

function App() {
  const [ color, setColor ] = useState('red');
  return (
    <div>
      <ToggleColorButton color={ color }/>
    </div>
  );
}
export default App;
```
여기서 한가지 !TIP 만약 color2가 있으면, 특별한 처리가 없을 경우, color를 쓰는 자식 컴포넌트또한 재랜더링될 것입니다. BUT! React.memo를 사용하면 사용는 속성이 변경되었을 경우에만 재랜더링될 것입니다.
```
function ToggleColorButton({ color }) {
  return (
    <button style={{ backgroundColor: color }}>
        색토글
    </button>
  );
}
export default React.memo(ToggleColorButton);

function App() {
  const [ color, setColor ] = useState('red');
  const [ color2, setColor2 ] = useState('red');
  const onColor2Toggle = () => setColor2('blue');
  return (
    <div>
      <ToggleColorButton color={ color }/>
      <br />
      <button style={{ backgroundColor: color2 }} onClick={ onColor2Toggle }>
        색토글
    </button>
    </div>
  );
}
```

### 2. 컴포넌트 함수의 반환값
컴포넌트는 react요소를 반환할 수 있고 return으로 jsx, 숫자, 문자, 배열, boolean, fragment등등을 반환할 수 있습니다.
하지만 배열을 반환할 경우에는 key를 꼭 갖고 있어야하고 react가 가상 돔에서 효율적으로 처리하기 위한것으로 항상 써주어야 합니다.
아니면 Warning: Each child in a list should have a unique "key" prop. 이 친구를 만날 수 있습니다. 
```
function App() {
  return [
    <div key='1번'> 1번 </div>, <div key='2번'> 2번 </div>
  ]
}
```
fragment는 진짜 유용한 필요한 녀석입니다. div없이 여러 요소를 넣어도 key가없는 에러가 나지 않는데 나열된 순서에 따라 key로서 작용된다고 합니다. 그래서 fragment를 쓰면 쓸때 없는 상위 div를 버릴 수 있고 fragment는 <>로 축약 표시할 수 있습니다. 
```
function App() {
  return (
    <React.Fragment>
      <div key='1번'> 1번 </div>
      <div key='2번'> 2번 </div>
    </React.Fragment>
  );
}
```

### 오늘은 여기서 마무리하겠습니다~

 


