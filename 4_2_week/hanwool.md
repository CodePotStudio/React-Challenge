# React에서 Select Box 컴포넌트 만들기

이번 글에서는 React에서 Select Box 컴포넌트를 만드는 방법을 알아보도록 하겠습니다.

# HTML에서 Select Box 구성요소

```jsx
<select name="choice">
	<option value="banana">바나나</option>
	<option value="apple">사과</option>
	<option value="orange">오렌지</option>
</select>
```

![/hanwool_assets/Screen_Shot_2021-05-09_at_9.36.27_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_9.36.27_PM.png)

기본적으로 select box 태그의 경우, 내부에 여러 option을 option 태그로 넣는 구조로 되어 있습니다.

이를 이제 React로 옮겨 보도록 하겠습니다.

# React에서 Select Box 만들기

```jsx
// App.js

const SelectBox = () => {
	return (
		<select>
			<option key="banana" value="banana">
				바나나
			</option>
			<option key="apple" value="apple">사과</option>
			<option key="orange" value="orange">오렌지</option>
		</select>
	);
};

const App = () => {
	return <>
		<SelectBox>
	</>
}
```

![/hanwool_assets/Screen_Shot_2021-05-09_at_9.36.27_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_9.36.27_PM.png)

react에서는 children 컴포넌트(위 코드에서는 select 아래에 있는 option 컴포넌트)에는 반드시 key props가 있어야 하므로, key props를 추가하여 주었습니다.

위와 같이 코드를 변경하고, 다시 실행하면 기존과 같이 `select box`가 렌더링 되는 것을 알 수 있습니다.

## select box 옵션을 props로 받기

기존의 코드는 `select box`의 옵션들을 하드코딩하여 넣어두었는데, 이제는 `option`을 `props`를 통해서 받아보도록 하겠습니다.

```jsx
// option들을 정의
const OPTIONS = [
	{ value: "apple", name: "사과" },
	{ value: "banana", name: "바나나" },
	{ value: "orange", name: "오렌지" },
];

const SelectBox = (props) => {
	return (
		<select>
			{props.options.map((option) => (
				<option key={option.value} value={option.value}>
					{option.name}
				</option>
			))}
		</select>
	);
};

function App() {
	// option을 SeletBox의 props로 내려주기
	return <SelectBox options={OPTIONS}></SelectBox>;
}

export default App;
```

`SelectBox` 컴포넌트에서 `options`를 `props`로 받아서 `SelectBox` 컴포넌트 내부에서 `map`을 통해서 `option` 컴포넌트를 하나씩 만들어 주었습니다.

## selectBox default value 추가하기

`selectBox`가 렌더링될 때 최초에 렌더링될 `default value`를 설정해 보도록 하겠습니다.

```jsx
// option들을 정의
const OPTIONS = [
	{ value: "apple", name: "사과" },
	{ value: "banana", name: "바나나" },
	{ value: "orange", name: "오렌지" },
];

const SelectBox = (props) => {
	return (
		<select>
			{props.options.map((option) => (
				<option
					value={option.value}
					defaultValue={props.defaultValue === option.value}
				>
					{option.name}
				</option>
			))}
		</select>
	);
};

function App() {
	return <SelectBox options={OPTIONS} defaultValue="banana"></SelectBox>;
}

export default App;
```

SelectBox 컴포넌트에서 defaultValue props를 받습니다.

그리고 option을 렌더링할 때 `defaultValue={props.defaultValue === option.value}` 를 통해서 props에서 받은 defaultValue와 option의 value를 확인하여 selected props의 값을 변경하여 줍니다.

![/hanwool_assets/Screen_Shot_2021-05-09_at_10.00.11_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_10.00.11_PM.png)

실행해보면 위와 같이 바나나가 최초 option으로 렌더링 되는 것을 알 수 있습니다.

## select box handler 추가하기

```jsx
...
// App.js
const SelectBox = (props) => {
	const handleChange = (e) => {
		// event handler
		console.log(e.target.value);
	};

	return (
		<select onChange={handleChange}>
			{props.options.map((option) => (
				<option
					key={option.value}
					value={option.value}
					defaultValue={props.defaultValue === option.value}
				>
					{option.name}
				</option>
			))}
		</select>
	);
};

...
```

select box도 button 컴포넌트처럼 onChange event를 통해서 선택된 값에 접근할 수 있습니다.

![/hanwool_assets/Screen_Shot_2021-05-09_at_10.03.02_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_10.03.02_PM.png)

선택된 값은 e.target.value 안에 포함되어 있으며, 위와 같이 선택할 때마다 선택된 옵션의 value 값이 찍힌 것을 알 수 있습니다.

# Select Box CSS 커스텀하기

select box의 사용방법을 알아보았으니, styled-component를 통해서 CSS를 변경해 보도록 하겠습니다.

우선 styled-component를 설치하도록 하겠습니다.

```jsx
yarn add styled-components
```

```jsx
// App.js
...
import styled from "styled-components";

// focus 시에 border-color를 red로 변경
export const Select = styled.select`
	margin: 0;
	min-width: 0;
	display: block;
	width: 100%;
	padding: 8px 8px;
	font-size: inherit;
	line-height: inherit;
	border: 1px solid;
	border-radius: 4px;
	color: inherit;
	background-color: transparent;
	&:focus {
		border-color: red;
	}
`;

const SelectBox = (props) => {
	const handleChange = (e) => {
		// event handler
		console.log(e.target.value);
	};

	return (
		<Select onChange={handleChange}>
			{props.options.map((option) => (
				<option
					key={option.value}
					value={option.value}
					defaultValue={props.defaultValue === option.value}
				>
					{option.name}
				</option>
			))}
		</Select>
	);
};
...
```

styled-components에서 styled를 import하고 우리만의 Select 컴포넌트를 생성해 줍니다.

위 코드를 보면, Select 컴포넌트 내부의 style로 selectbox를 focus 시에 border를 빨간색으로 변경하였습니다.

![/hanwool_assets/Screen_Shot_2021-05-09_at_10.13.09_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_10.13.09_PM.png)

실행해 보면 위와 같이 style이 변경된 것을 알 수 있습니다.

이외에도 다양한 스타일을 추가할 수 있습니다.

## Select Box 아이콘 변경하기

select box의 경우, 각 브라우저 별로 default Icon이 정해져 있습니다. 이제는 이 Icon을 우리가 원하는 icon으로 변경해 보도록 하겠습니다.

```jsx
// App.js
...
import styled from "styled-components";

// 기본 icon 비활성화
export const Select = styled.select`
	...
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
	...
`;
...
```

위와 같이 style을 추가하면 아래처럼 방향 화살표가 없어진 것을 알 수 있습니다.

![/hanwool_assets/Screen_Shot_2021-05-09_at_10.20.04_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_10.20.04_PM.png)

이제 이 곳에 우리의 아이콘(아래 방향 삼각형 아이콘)을 넣어 보도록 하겠습니다.

```jsx
// App.js
...
// flex Box를 사용하기 위해 display flex option을 넣은 Wrapper를 하나 생성
const SelectBoxWrapper = styled.div`
	display: flex;
`;

// Icon에 사용할 Icon SVG 컴포넌트 생성
const IconSVG = styled.svg`
	margin-left: -28px;
	align-self: center;
	width: 24px;
	height: 24px;
`;

const SelectBox = (props) => {
	...
	return (
		<SelectBoxWrapper>
			<Select onChange={handleChange}>
				{props.options.map((option) => (
					<option
						key={option.value}
						value={option.value}
						defaultValue={props.defaultValue === option.value}
					>
						{option.name}
					</option>
				))}
			</Select>
			<IconSVG
				width="20"
				height="20"
				viewBox="0 0 20 20"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					fill-rule="evenodd"
					clip-rule="evenodd"
					d="M10 14L16 6H4L10 14Z"
					fill="#1A1A1A"
				/>
			</IconSVG>
		</SelectBoxWrapper>
	);
};

...
```

변경된 코드 내용

- Select Box 내에서 display: flex를 사용하기 위해 SelectBoxWrapper 컴포넌트를 추가
- svg 태그에 스타일을 추가하기 위해 IconSVG 컴포넌트를 추가 (위치 설정, 아이콘 크기, 정렬)

위와 같이 코드를 변경하고 실행해 보도록 하겠습니다.

![/hanwool_assets/Screen_Shot_2021-05-09_at_10.29.10_PM.png](/hanwool_assets/Screen_Shot_2021-05-09_at_10.29.10_PM.png)

위 화면처럼 우리가 원하는 대로 아이콘이 변경된 것을 알 수 있습니다.

이로써 간단하게 Select Box 컴포넌트를 만드는 방법을 알아 보았습니다. 이외에도 React에서는 Select Box를 쉽게 만들 수 있는 라이브러리가 존재합니다. ([링크](https://react-select.com/home))

직접 커스터마이징하여 쓰기에는 시간이 부족하다고 생각되시면 위 라이브러리를 써보시는 것도 추천드립니다.

# 전체 코드 살펴보기

- github에서 전체 코드 보기 ([링크](https://github.com/toy-crane/make-select-box))

![/hanwool_assets/Untitled.png](/hanwool_assets/Untitled.png)

이 블로그 글은 Code Pot, 리액트 챌린지의 과제로 작성되었습니다.

Code Pot, 리액트 챌린지가 궁금하다면? ⇒ [링크](https://www.notion.so/Code-Pot-React-2-a299e9ab5e4c4b97ae59028a90db9720)
