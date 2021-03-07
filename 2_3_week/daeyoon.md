# SPA에 대하여...

## SPA vs MPA
간단하게 MPA에서는 클라인언트가 요청을 보내면 HTML을 보내준다. 클라이언트는 페이지를 리로드하게 됩니다.
SPA는 최초에 HTML을 내려주지만 필요할때만 데이터를 받아서 처리하며 페이지 전환등은 JS처리해서 그려준다.

### 페이지 전환등은 JS처리해서 그려준다?
즉, SPA가 가능하려면 페이지 전화 앞으로가기, 뒤로가기, 등이 JS로 처리할 수 있어야합니다.
그래서 존재하는것이 pushState, reaplaceState의 History 인터페이스를 사용하여 처리할 수 있습니다.
History 인터페이스는 브라우저의 현재 페이지를 불러온 탭 또는 프레임의 방문 기록을 조작할 수 있는 방법을 제공합니다.
또한 popState 이벤트를 통하여 해결하는데 브라우저의 백 버튼 등의 브라우저에 의해서 발생하는것 입니다.

이것을 쉽게 해주는 라이브러리가 react-router-dom 입니다. 사용만하고 살펴보지는 않았던 라이브러인데 전부는 아니지만 핵심적으로 어떻게 구현되어 움직이는가에 대하여 알아보겠습니다.

## 간단하게 router처럼 움직일 수 있도록 구현해보기!
~~~
function App() {
	useEffect(() => {
		window.onpopstate = (event) => {
			console.log(`location: ${document.location}, state: ${event.state}`);
		};
	},[]);
	return (
		<div>
			<button onClick={() => window.history.pushState('v1', '', '/page1')}>
				page1
			</button>
			<button onClick={() => window.history.pushState('v2', '', '/page2')}>
				page2
			</button>
		</div>
	);
}
~~~
- pushState 파라미터들에 대한 설명입니다. 1. 데이터=event.state,  2.title,  3.url
- 이렇게만 구현해도 버튼을 클릭했을경우, 서버로 요청이 가지않고도 url이 변경되는것을 확인할 수 있으며,
- 브라우저의 <- -> 을 눌렀을 때 onpopstate의 이벤트가 적절히 실행되는것을 확인할 수 있습니다. 

### 여기에 실제 처럼 페이지 컴포넌트들을 붙혀 라우터이동 해보기!
~~~
function App() {
	const [pageName, setPageName] = useState('');

	useEffect(() => {
		window.onpopstate = (event) => {
			setPageName(event.state);
		};
	},[]);

	const goPage1 = () => {
		const pageName = 'page1';
		window.history.pushState(pageName, '', '/page1');
		setPageName(pageName);
	}

	const goPage2 = () => {
		const pageName = 'page2';
		window.history.pushState(pageName, '', '/page2');
		setPageName(pageName);
	}

	return (
		<div>
			<button onClick={goPage1}>
				page1
			</button>
			<button onClick={goPage2}>
				page2
			</button>
			{!pageName && <Home />}
			{pageName === 'page1' && <First />}
			{pageName === 'page2' && <Second />}
		</div>
	);
}
function Home() {
    return <h2> This is Home </h2>
}

function First() {
    return <h2> This is First Page </h2>
}

function Second() {
    return <h2> I am Second </h2>
}
~~~
- 위 코드처럼 pageName란 상태값을 통해 현재 라우터에 대한 페이지를 그릴 수 있도록 구현해 보았습니다.
- 페이지를 이동할 경우, 상태를 변경하여 페이지를 변경하고, 
- history사용으로 인해 브라우저 UI를 통한 변경에서도 정상동작하는것을 볼 수 있습니다.
- 극히 일부분만을 구현했지만 구현해 놓고 보니 생각보다 쉽게 구현할 수 있었습니다.

#### 하지만 여기에서는 query, props 전달 및 url검색을 통한 페이지 맵핑등과같은것은 빠져있어 더 고민하고 구현을 해야합니다.

## TIP!
### 동적 import를 통해서 해당 파일이 필요한 상황에 불러올 수 있도록하는 방법!
~~~
	function onClick() { 
		//동적 import 
		import('./data.json').then(( { default: data} ) => {
			console.log(data);
		});
	}
~~~



