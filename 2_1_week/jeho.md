# setState()

setState()는 state를 업데이트 시켜주는 함수입니다

state는 리액트에서 컴포넌트 내부에서 관리되는 상태값을 나타내는 객체이고 이 값이 변경된다면 값을 가지고 있는 컴포넌트는 리렌더링됩니다

다음은 증가 버튼을 클릭하면 setNumber에 의해 number가 1이 증가되는 컴포넌트입니다

```jsx
const Counter = () => {
    const [number, setNumber] = useState(0);

    const handleClick = () => {
        setNumber(number+1); // 0+1 => 1
    };

    return (
        <div>
            <div>{number}</div>
            <button onClick={handleClick}>증가</button>
        </div>
    );
}
```

이 경우에는 제가 원하는대로 1번의 증가마다 숫자가 1씩 증가하도록 잘 작동합니다

이번엔 한번의 증가로 number가 3씩 늘어나게끔 수정해보도록 하겠습니다

```jsx
const Counter = () => {
    const [number, setNumber] = useState(0);

    const handleClick = () => {
        setNumber(number+1); 
        setNumber(number+1); 
        setNumber(number+1); 
    };

    return (
        <div>
            <div>{number}</div>
            <button onClick={handleClick}>증가</button>
        </div>
    );
}
```

위의 코드에서 처음 증가를 눌렀을 때 바라는 작동 방식은 다음과 같습니다

```jsx
    const handleClick = () => {
        setNumber(number+1); // 0+1 => 1
        setNumber(number+1); // 1+1 => 2
        setNumber(number+1); // 2+1 => 3
    };
```

이처럼 number가 3으로 리렌더링되길 바라고 눌렀지만 값은 1이 나옵니다

## 왜 1 일까요?

클래스형 컴포넌트라는 것을 제외하고 거의 동일한 예제를 사용한 리액트 공식 홈페이지의 설명에 따르면 그 이유는 다음과 같습니다

- handleClick 함수안에서 사용되는 number는 Counter 컴포넌트의 state값인 number입니다
(현재 number의 값은 0입니다)
- Counter 컴포넌트가 리렌더링되기 전까지 number의 값은 변하지 않습니다
- hanldeClick함수에서 사용하는 3번의 setNumber 파라미터 number의 값은 모두 0입니다

위 설명대로라면 제가 짠 코드는 아래처럼 돌아가고 있습니다

```jsx
    const handleClick = () => {
        setNumber(number+1); // 0+1 => 1
        setNumber(number+1); // 0+1 => 1
        setNumber(number+1); // 0+1 => 1
    };
		// 이후 handleClick함수가 끝나면 number값이 1로 바뀌면서 리렌더링됨
```

이렇게 해서 number가 1로 출력되는 것입니다

## setState의 비동기적 업데이트

제일 처음 배우기를 state의 값이 바뀌면 해당 컴포넌트가 리렌더링된다고 배웠습니다

그런데 왜 handleClick안에서 첫 setNumber가 호출됐을 때 number의 값이 1로 바뀌는데 바로 리렌더링되지 않을까요?

이유는 대략 다음과 같습니다

- 이벤트 핸들러 내에서 setState는 일괄적으로 처리됩니다

    ⇒ 만나는 setState들은 바로 실행되는 것이 아닌 대기열에 보내서 나중에 일괄적으로 처리합니다

- setState로 인해 state값이 바뀔 때 컴포넌트에게 즉각적으로 렌더링을 요구하지 않습니다

    ⇒ 그렇기 때문에 일괄적으로 setState가 처리될 때 렌더링이 여러번 일어나지 않습니다

이를 바탕으로 위의 코드를 다시 살펴보면 아래와 같습니다

<img width="688" alt="1" src="https://user-images.githubusercontent.com/45571631/108616797-39291280-7454-11eb-9d1b-cfc8ed243b39.png">

---

이렇게 비동기적 업데이트를 하기 때문에 setNumber를 부른 후 바로 number에 접근하면 원하는 결과값을 얻을 수 없는 것은 당연해보입니다

이러한 비동기적 업데이트는 다중 렌더링에 의한 성능 저하를 방지합니다

## updater 함수 사용하기


그럼 어떻게 state에 바로 갱신하고 갱신된 state에 접근해서 조작할 수 있을까요?

setState에는 사용법이 하나 더 존재하는데 바로 첫 번째 인자로 원시 값이나 객체가 아니라 함수를 넘겨주는 방법입니다

```jsx
setState((state, props) => stateChange);
```

이 콜백 함수에서 받아오는 state와 props는 setState가 실행되는 그 시점에서 컴포넌트가 가지는 값 즉, 가장 최신값입니다

콜백 함수의 반환값은 컴포넌트의 state값에 즉시 얕게 병합됩니다

```jsx
const handleClick = () => {
        setNumber(number=>{
            console.log(number); // number : 0  
            return number+1;
        });
        setNumber(number=>{
            console.log(number); // number : 1
            return number+1;
	        });
        setNumber(number=>{
            console.log(number); // number : 2
            return number+1;
        });
    };
```

그림으로 보면 다음과 같습니다

<img width="632" alt="대기" src="https://user-images.githubusercontent.com/45571631/108616781-13037280-7454-11eb-8d74-554cbbdc4cb3.png">

---

1. 대기열에 setNumber가 들어갑니다
2. setNumber의 updater 함수에서 현재 number값을 컴포넌트에서 받아옵니다 (number : 0)
3. setNumber에 의해 업데이트된 number가 Counter 컴포넌트의 number에 병합됩니다 (number : 1)

_공부하다 보니 업데이트되는 리액트에서는 이벤트 핸들러 내부에서만이 아닌 어디서든 setState가 일괄 처리된다고 나와있는데 현재 버전의 리액트에서는 어떤지 모르겠습니다_

