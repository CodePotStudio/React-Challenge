# React vs Vanilla 비교하기~!
항상 왜 React로 개발해야하는가에 대하여 그냥 많은 라이브러리 들로 편하게 개발하고 가상 돔으로 효율적이고 컴포넌트를 통해서 재사용성있게 개발할 수 있고 등등... 여러가지 이유를 암기하여 기억하고 느낌적으로 알고 있지만 실제로 이래서 React쓰는 구나!에 대해서 명확하게 요렇게 Vanilla랑 다르구나에 대해서 알아보겠습니다!
## 각각의 방법으로 Todo APP 개발 해보기!
*간단한 비교를 위해서 CSS 및 기타 사항들은 제거하고 MVP만을 개발해보고 비교하겠습니다.

### 먼저 Vanilla로 구현해 보겠습니다. 
~~~
	<!-- DOM부분 입니다. -->
	<div class="todo">
		<h3>Todos</h3>
		<ul class="list"></ul>
		<input type="text" class="description" />
		<button onclick="onAdd()"> 추가 </button>
	</div>
~~~

~~~
// script 부분입니다. 
const onAdd = () => {
	const descriptionElement = document.querySelector('.todo .description');
	const todo = { 
		id: currentId++,
		description: descriptionElement.value
	}
	todos.push(todo);
	const listElement = document.querySelector('.todo .list');
	const todoElement = makeTodoElement(todo);
	listElement.appendChild(todoElement);
};

const makeTodoElement = (todo) => {
	const todoElement = document.createElement('li');
	const spanElement = document.createElement('span');
	const buttonElement = document.createElement('button');
	spanElement.innerHTML = todo.description;
	buttonElement.innerHTML = '삭제'
	buttonElement.dataset.id = todo.id;
	buttonElement.onclick = onDelete;
	todoElement.appendChild(spanElement);
	todoElement.appendChild(buttonElement);
	return todoElement;
}
~~~


### 다음으로 React를 구현해 보았습니다!
~~~
function App() {

	const [todos, setTodos] = useState([]); // 1: 상태값, 2: 상태값 변경 함수 []로 초기화
	const [currentId, setCurrentId] = useState(1);
	const [description, setDescription] = useState('');
	const [isShowOdd, setIsShowOdd] = useState(false);
	
	const onAdd = () => {
		const todo = {id: currentId, description};
		setCurrentId(currentId + 1);
		setTodos([...todos, todo ]);
	};

	const onDelete = (event) => {
		const id = Number(event.target.dataset.id)
		const newTodos = todos.filter(todo => todo.id !== id);
		setTodos(newTodos);
	};
	
	const isShowOddTest = () => isShowOdd ? '활성' : '비활성';

  	return (
		<div>
			<h3>Todos</h3>
			<ul>
				{todos
				.filter( (_,index) => isShowOdd ? index % 2 === 0 : true)
				.map(todo => (
					<li key={ todo.id }>
						<span>{ todo.description }</span>
						<button data-id={ todo.id } onClick={ onDelete }> 삭제 </button>
					</li>
				))}
			</ul>
			<input type="text" value={ description } onChange={e => setDescription(e.target.value)} />
      		<button onClick={onAdd}> 추가 </button>
			  <button onClick={() => setIsShowOdd(!isShowOdd) }> 홀수만 { isShowOddTest() } </button>
		</div>
  	);
}
~~~

## 비교하기!!
먼저, 간단한 Todo APP으로 Vanilla로 구현해도 크게 무리는 없고 코드를 따라가다 보면 코드를 쉽게 이해할 수 있고 React로 구현한 것과 비교해서 어려운 크게 어려운 부분이 없었습니다. 그럼에도 큰차이를 알 수 있었습니다. 

### Vanilla는!
- Vanilla 구현부를 보게되면 이벤트 핸들러 부분에서 DOM을 즉, UI를 직접 조작하고 있습니다. 
- 물론 당현한 말이지만 이것은 과거의 DOM의 구조를 통해 미래의 DOM의 구조를 파악하기 매우 힘듭니다
- 왜냐하면 이벤트 핸들러 부분에서 어떻게 변화하는지에 대하여 확인이 필요하고 이에 따라 예측과 디버깅이 어렵습니다.

### 하지만 React는!
- React 구현부를 보면 비즈니스 로직과 UI로직이 명확히 구분되어 있습니다. JSX로 작성하면 UI에 대한 것은 비즈니스 로직 구현부에서는 많이 확인할 필요가 없습니다. 또한 Vanilla에서 문제가 되었던 미래의 DOM의 구조에 대해서 예측이 가능할 수 있어 생상성에 지대한 영향을 줄 것입니다. 
- 추후에 홀수만 보는 필터 기능을 추가할 떄도 명확하고 간단하게 구현이 가능했습니다. 하지만 Vanilla에선...좀 하려면 할 수 있겠지만 조금 지저분하게 구현해야되며 무엇 보다도 UI를 직접 핸들링하기 떄문에 비즈니스로직만을 재사용함에 있어 어려움이 있습니다. 

### 마무리하며...
이번 비교를 통해서 react-router, redux 등등 라이브러리, 가상 돔 좋지만 'React 왜 사용하세요?' 라는 질문을 듣게 되면 가장 먼저 비즈니스 로직과 UI로직이 분리되어 있는 것에 대한 이점에 대해 명확히 대답하고 주변에 또는 시작하려는 사람들에게 사용을 권유할 수 있을 것 같습니다!
