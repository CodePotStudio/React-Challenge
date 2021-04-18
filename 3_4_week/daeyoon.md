## React Context API

### Context API 왜 쓰죠? 바로 전역 상태 관리
- react에서 상위 컴포넌트에서 하위 컴포넌트로 속상값을 전달합니다. 
- 하지만 이것이 복잡하게 되고 여러번에 걸처 전파되고 반복된다면? 너무 귀찬고 코드도 반복되게 됩니다.
- 이때 Context API를 사용하게 된다면 좀 더 간편하게 코드를 작성할 수 있습니다.

#### 주의 사항 
render-props패턴으로 자식을 함수을 호출하는 형태로 작성을 해서 사용됬을때 필요한 값을 찾게되는데 가장 
부모로 올라가면서 가장 가까운 provider를 사용할게 됩니다. root까지 갔는데 못가면 default값을 사용하게됩니다.

### 컴포넌트에서 데이터 사용하기
```
//createContext 함수를 통해 생성해서 사용할 수 있습니다.
const ContextPeople = createContext("초기값");

const App = () => { 
	const [name, setName] = useState("윤")
    
    // Provider에서 value에 값을 넣어주면 Consumer에서 값을 받아서 사용할 수 있습니다.
	return (
		<ContextPeople.Provider value={name}>
			<People />
		</ContextPeople.Provider>
	);
}


const People = () => {
	return (
		<div>
			<Hello />
		</div>
	)
}
const Hello = () => {
	return (
		<ContextPeople>
			{/* render-props패턴 함수를 호출하는 형태로 구현 */}
			{name =>  <p>{`${name}님이 등장하셨습니다. 부대 차렸.`}</p> }
		</ContextPeople>
	)
}
// 이렇게 변경이 가능합니다. 위 처럼 쓰면 비즈니스로직에서 name을 사용을 못하기 떄문입니다.

const Hello = () => {
	const name = useContext(ContextPeople);
	return (
		<p>{`${name}님이 등장하셨습니다. 부대 차렸.`}</p>
	)
}
```
### 컴포넌트에서 데이터 변경하기
```
//...생략
const ContextPeople = createContext("초기값");
const ContextSetPeople = createContext(() => {});

const App = () => { 
	const [name, setName] = useState("윤")
	return (
		<ContextPeople.Provider value={name}>
			<ContextSetPeople.Provider value={setName}>
			<People />
			</ContextSetPeople.Provider>
		</ContextPeople.Provider>
	);
}

const People = React.memo(() => {
	const name = useContext(ContextPeople);
	const setName = useContext(ContextSetPeople);
	return (
		<div>
			<Hello />
			<input type="text" value={name} onChange={event => setName(event.target.value)} />
		</div>
	)
})
//...생략
```

## !TIP
- 대부분의 경우 Root에서 전채를 감싸서 사용하는 방식으로 사용합니다. 그래서 못찾게 하는것입니다.
- Provider 컴포넌트의 값이 변경이 되면 하위의 모든 컴포넌트들이 모두 랜더링 됩니다.
    -  Context API를 사용할 경우에는 데이터 종류별로 분리하여 사용해서 Rendering을 주릴 수 있어야합니다.
- Context.Provider에 value를 넣어줄떄 객체를 넣어준다면?
    - 컴포넌트가 만약 재랜더링이 일어날때마다 데이터를 넣어주어 의미없이 재랜더링이 될 수 있습니다.
    - 물론 이렇게 할일은 많이 없지만 조심해야합니다.
```
    const people = useState({name: 'yoon'})

    ContextPeople.Provide value={{name: people.name}}
```

## 마무리...
상태관리 라이브러리들이 많이 사용되고 있습니다. (Redux, Mobx 등등) 그렇다면 라이브러리들은 왜 사용하고 Context API는 왜 사용하는가? 첫번째는 바로 전역 상태 관리 때문입니다. 그리고 Redux는 Context API를 기반으로 만들어 졌습니다.

#### 그렇다면 Context API를 통해서 만들어진 그리고 잘 만들어진 라이브러리들만 사용하면 되는거 아닌가?

제 답은 반반입니다. Redux를 기준으로는 Redux는 상태 관리 이외의 여러 부가기능을 재공합니다. Dev Tools, 상태 변화 추적, 그리고 찾아보니 엄청 잦은 빈도로 업데이트가 일어날 경우, Redux가 더 좋고 추가적인 상태관리 라이브러리가 사용가능합니다. 

하지만 오직 전역 상태 관리를 위해서는 Context API를 사용하는 것이 좋아보입니다. Redux는 Context API에 비해 코드 수가 길며, 또 러닝 시간이 걸립니다.

즉, 부가 기능이 필요하다면 라이브러리를 아니면 Context API를 쓰는 것이 좋습니다.

### Tool 도구를 너무 남용하지말고, 상황에 따라서 가벼울 때, 무거울 때를 생각하고 사용해야합니다!

