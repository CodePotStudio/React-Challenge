# placehoder Loading 컴포넌트 만들기

![placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/34308760-ec55df82-e735-11e7-843b-2e311fa7b7d0.gif](placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/34308760-ec55df82-e735-11e7-843b-2e311fa7b7d0.gif)

이 번 포스팅에서는 `react-content-loader` 를 활용해서 위와 같은 place holder loading을 만들어 볼 생각입니다.

## Sample 카드 만들기

![placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Screen_Shot_2021-03-14_at_9.23.55_PM.png](placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Screen_Shot_2021-03-14_at_9.23.55_PM.png)

우선 위 이미지와 같은 샘플 카드를 하나 만들어 보도록 하겠습니다.

`styled Component`를 통해서 구현했으며, `styled-component`에 대해서 알고 싶은신 분은 [링크](https://styled-components.com/)를 통해서 좀더 자세히 확인 하실 수 있습니다.

```jsx
//App.js
import styled from "styled-components";
import "./App.css";
import Card from "./Card";

const AppContainer = styled.div`
	width: 1024px;
	margin: 0 auto;
`;

const CardContainer = styled.div`
	display: grid;
	grid-gap: 16px;
	grid-template-columns: repeat(2, 1fr);
`;

function App() {
	return (
		<AppContainer>
			<h1>CARD Sample</h1>
			<CardContainer>
				<Card></Card>
				<Card></Card>
				<Card></Card>
				<Card></Card>
			</CardContainer>
		</AppContainer>
	);
}

export default App;
```

```jsx
// Card.js
import styled from "styled-components";

const CardContainer = styled.div`
	display: flex;
	flex-direction: column;
	border: 1px solid;
	border-color: #e4e4e4;
	background-color: white;
	border-radius: 8px;
	padding: 8px;
`;

const TitleSection = styled.div`
	display: flex;
	align-items: center;
`;

const Avatar = styled.img`
	margin: 0;
	min-width: 0;
	max-width: 100%;
	height: auto;
	border-radius: 9999px;
	width: 36px;
	height: 36px;
	border-radius: 99999px;
	display: block;
`;

const Title = styled.h2`
	margin: 0;
	min-width: 0;
	font-size: 16px;
	line-height: 1.6;
	font-weight: 700;
	margin-left: 16px;
`;

const Article = styled.p`
	color: #000;
	font-size: 14px;
	margin-bottom: 16px;
	font-size: 16px;
`;

const Card = () => (
	<CardContainer>
		<TitleSection>
			<Avatar src="https://avatars1.githubusercontent.com/u/3426196?v=4"></Avatar>
			<Title>Toy-Crane | 개발자</Title>
		</TitleSection>
		<Article>
			풀이 그들에게 무엇을 귀는 이것을 따뜻한 만천하의 광야에서 사막이다. 자신과
			능히 속에 보배를 그들은 없으면, 석가는 교향악이다. 군영과 그들은 하는
			생명을 이성은 튼튼하며, 들어 청춘의 충분히 것이다. 대고, 이상은 수 역사를
			밝은 못할 얼음 같으며, 끓는 것이다. 이상은 것이다.보라, 위하여서 든 인간에
			청춘에서만 것이다. 청춘의 보배를 든 평화스러운 따뜻한 얼마나 실로 인생에
			피다. 새가 청춘을 밥을 이상이 피가 이 간에 싸인 있는가? 귀는 크고 투명하되
			희망의 트고, 이상의 쓸쓸하랴? 풀이 얼음이 못할 주는 없으면 피가 역사를
			것이다.
		</Article>
	</CardContainer>
);

export default Card;
```

## react-content-loader

react-content-loader는 SVG 기반으로 로더를 만들어주는 라이브러리입니다. 우리가 만들 SVG를 기반으로 loader를 만들 수도 있고, 아니면 라이브러리 내에서 자체적으로 지원하는 Facebook, Instagram placeholder를 활용할 수 있습니다.

### 패키지 설치하기

```jsx
yarn add react-content-loader
```

### 페이스북 placehoder 사용하기

```jsx
// App.js

import ContentLoader, { Facebook } from 'react-content-loader'
import styled from "styled-components";
import "./App.css";
import Card from "./Card";
import { Facebook } from "react-content-loader";

const AppContainer = styled.div`
	width: 1024px;
	margin: 0 auto;
`;

const CardContainer = styled.div`
	display: grid;
	grid-gap: 16px;
	grid-template-columns: repeat(2, 1fr);
`;

function App() {
	return (
		<AppContainer>
			<h1>CARD Sample</h1>
			<Facebook></Facebook>
		</AppContainer>
	);
}

export default App;
```

기본 placehoder를 사용하는 것은 어렵지 않습니다. `react-content-loader` 에서 원하는 place holder 컴포넌트를 import하고, 원하는 곳에 렌더링 합니다. 그렇게 되면 svg 이미지 기반 때문에, 감싸고 있는 컨테이너의 사이즈에 맞추어 자동으로 크기가 조절이 됩니다.

![placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Kapture_2021-03-14_at_21.44.22.gif](placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Kapture_2021-03-14_at_21.44.22.gif)

위와 같이 간단하게 Facebook 스타일의 placeholder가 적용된 것을 알 수 있습니다.

## Custom PlaceHolder 만들기

커스텀 place holder를 만들기 위해서 [링크](https://skeletonreact.com/) 사이트에서 우리가 원하는 사이즈의 svg 이미지를 만들어야 합니다.

![placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Screen_Shot_2021-03-14_at_9.57.48_PM.png](placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Screen_Shot_2021-03-14_at_9.57.48_PM.png)

사이트에 접속하면 오른쪽 하얀색 화면에서 우리가 원하는 사이즈로 placeholder를 만들 수 있습니다. 전체 canvas의 size를 비율에 맞게끔 추가해주고, 원하는 형태의 placehoder를 만들 수 있습니다.

그런데 아쉬운 점은 되돌리기가 되지 않아서 작업하면서 계속 실수를 하게 되는 아쉬운 점이 있습니다. 직접 SVG를 생성하는 Application을 쓰고 계시는 것이 있다면 svg를 만들고 import 하는 것을 추천 드립니다.

```jsx
// Card.js
import React from "react"
import ContentLoader from "react-content-loader"

...
const CardLoader = (props) => (
  <ContentLoader 
    speed={2}
    width={496}
    height={208}
    viewBox="0 0 496 208"
    backgroundColor="#f3f3f3"
    foregroundColor="#ecebeb"
    {...props}
  >
    <rect x="69" y="16" rx="3" ry="3" width="88" height="12" /> 
    <rect x="69" y="36" rx="3" ry="3" width="52" height="13" /> 
    <rect x="4" y="65" rx="3" ry="3" width="410" height="24" /> 
    <rect x="4" y="103" rx="3" ry="3" width="410" height="24" /> 
    <circle cx="34" cy="31" r="18" /> 
    <rect x="4" y="142" rx="3" ry="3" width="410" height="24" /> 
    <rect x="5" y="180" rx="3" ry="3" width="410" height="24" />
  </ContentLoader>
)
...

const Card = ({ loading }) =>
	loading ? (
		<MyCardLoader />
	) : (
		<CardContainer>
			<TitleSection>
				<Avatar src="https://avatars1.githubusercontent.com/u/3426196?v=4"></Avatar>
				<Title>Toy-Crane | 개발자</Title>
			</TitleSection>
			<Article>
				풀이 그들에게 무엇을 귀는 이것을 따뜻한 만천하의 광야에서 사막이다.
				자신과 능히 속에 보배를 그들은 없으면, 석가는 교향악이다. 군영과 그들은
				하는 생명을 이성은 튼튼하며, 들어 청춘의 충분히 것이다. 대고, 이상은 수
				역사를 밝은 못할 얼음 같으며, 끓는 것이다. 이상은 것이다.보라, 위하여서
				든 인간에 청춘에서만 것이다. 청춘의 보배를 든 평화스러운 따뜻한 얼마나
				실로 인생에 피다. 새가 청춘을 밥을 이상이 피가 이 간에 싸인 있는가? 귀는
				크고 투명하되 희망의 트고, 이상의 쓸쓸하랴? 풀이 얼음이 못할 주는 없으면
				피가 역사를 것이다.
			</Article>
		</CardContainer>
	);

```

```jsx
import styled from "styled-components";
import "./App.css";
import Card from "./Card";
import { Facebook } from "react-content-loader";

const AppContainer = styled.div`
	width: 1024px;
	margin: 0 auto;
`;

const CardContainer = styled.div`
	display: grid;
	grid-gap: 16px;
	grid-template-columns: repeat(2, 1fr);
`;

function App() {
	return (
		<AppContainer>
			<h1>CARD Sample</h1>
			<CardContainer>
				<Card loading={true}></Card>
				<Card loading={true}></Card>
				<Card loading={true}></Card>
				<Card loading={true}></Card>
			</CardContainer>
		</AppContainer>
	);
}

export default App; 
```

위와 같이 `CardLoader` 컴포넌트를 만들고, `loading` 상태에 따라 렌더링을 달리하는 코드를 만들어 보도록 하겠습니다.

```jsx
import { useEffect, useState } from "react";
import styled from "styled-components";
import "./App.css";
import Card from "./Card";

const AppContainer = styled.div`
	width: 1024px;
	margin: 0 auto;
`;

const CardContainer = styled.div`
	display: grid;
	grid-gap: 16px;
	grid-template-columns: repeat(2, 1fr);
`;

function App() {
	const [loading, setLoading] = useState(true);

	useEffect(() => {
		const timeout = setTimeout(() => setLoading((loading) => !loading), 1000);
		return () => clearTimeout(timeout);
	}, [setLoading]);

	return (
		<AppContainer>
			<h1>CARD Sample</h1>
			<CardContainer>
				<Card loading={loading}></Card>
				<Card loading={loading}></Card>
				<Card loading={loading}></Card>
				<Card loading={loading}></Card>
			</CardContainer>
		</AppContainer>
	);
}

export default App;
```

`loading` state를 추가해주고, 1초가 지난 후에 `loading` state를 false로 변경하는 코드를 만들었습니다.

그럼 이제 다시 한 번 실행해보도록 하겠습니다.

![placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Kapture_2021-03-14_at_22.39.24.gif](placehoder%20Loading%20%E1%84%8F%E1%85%A5%E1%86%B7%E1%84%91%E1%85%A9%E1%84%82%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%20%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5%2098c31c4f688c447d9983e147003a7a7d/Kapture_2021-03-14_at_22.39.24.gif)

위의 그림처럼 페이지 새로고침을 했을 때, 우리가 의도했던 대로 cardplace holder가 뜬 이후, 카드가 생성되는 것을 알 수 있습니다. 👏👏👏

이 블로그 글은 Code Pot, 리액트 챌린지의 과제로 작성되었습니다.

Code Pot, 리액트 챌린지가 궁금하다면? ⇒ [링크](https://www.notion.so/Code-Pot-React-2-a299e9ab5e4c4b97ae59028a90db9720) 

## 전체 코드

([github 링크](https://github.com/toy-crane/place-holder-loading))에서 전체 코드를 확인 하실 수 있습니다.

## 참고 자료

[https://medium.com/front-end-weekly/react-user-experience-tactics-placeholder-loading-content-fb734da0f9ae](https://medium.com/front-end-weekly/react-user-experience-tactics-placeholder-loading-content-fb734da0f9ae)