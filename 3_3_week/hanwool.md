# 5분 만에 알아보는 Storybook

스토리북 라이브러리르 사용하기 이전에는 CSS 코드를 변경하면, 변경된 컴포넌트를 확인하기 위해 컴포넌트를 사용한 곳에 가서 변경된 내용을 확인해야 했습니다. 하지만 Storybook 라이브러리를 활용하면 좀 더 효율적으로 작업환경을 개선 할 수 있습니다.

이번 시간에는 Storybook 라이브러리를 활용하여 버튼 컴포넌트를 독립적으로 렌더링하고, 쉽게 변경된 내용을 확인할 수 있는 환경을 만들어 보도록 하겠습니다.

## React 프로젝트 만들기

```jsx
npx create-react-app storybook-tutorial
```

## Storybook 설치하기

```jsx
npx sb init
```

## Storybook 실행하기

```jsx
npm run storybook
```

storybook을 설치했으니, 실행하여 어떤 구조로 구성되어 있는지 살펴보도록 하겠습니다.

![./hanwool_assets/Screen_Shot_2021-04-06_at_3.29.31_PM.png](./hanwool_assets/Screen_Shot_2021-04-06_at_3.29.31_PM.png)

실행하면 [localhost:6006](http://localhost:6006) 주소로 storybook 웹페이지에 접근할 수 있습니다.

storybook을 설치하면 storybook 라이브러리에서 설정해 놓은 예시 데이터들을 볼 수 있습니다. 외쪽 Button → Primary 탭으로 들어가면 하단 컨트롤 탭에서 props를 변경할 수 있고, 이에 따라 UI가 어떻게 바뀌는지 손쉽게 테스트가 가능한 것을 알 수 있습니다.

이제 이 버튼을 우리가 만들 버튼으로 바꿔 보도록 하겠습니다.

우선 시작하기 전에 Storybook 라이브러리에서 자동 생성하는 stories 폴더를 지워 주세요.

## Button 컴포넌트 만들기

```jsx
// src/components/Button/index.js

import React from "react";
import styled from "styled-components";

const StyledButton = styled.button`
	color: #ffffff;
  background-color: #7362ff;
	border-radius: 5px;
	border: 0px;
	height: 56px;
	padding: 4px;
	margin: 4px 0px;
	cursor: pointer;
	width: 100%;
	outline: none;
	font-weight: 700;
	&:hover {
    background-color: #a99fee;
	}
}
`;

const Button = (props) => <StyledButton>{props.children}</StyledButton>;
export default Button;
```

위와 같이 우리만의 `Button` 컴포넌트를 하나 생성 합니다.

## Storybook에서 버튼 만들기

그리고 이제 우리가 만든 버튼을 렌더링할 storybook 파일을 하나 만들어 보도록 하겠습니다. storybook 파일을 만들 때에는 반드시, *.stories.js 형태로 파일을 생성해야 합니다. 그래야 storybook 라이브러리가 storybook 파일임을 확인하고, 렌더링을 해줍니다.

```jsx
// src/components/Button/index.stories.js
import React from "react";
import Button from "./";

// 사용할 컴포넌트에 대한 명세
export default {
	title: "Components/Button",
	component: [Button],
};

// 외부에 보여질 컴포넌트
export const StyledButton = () => <Button>Hello</Button>; 
```

위와 같이 파일을 하나 만들어 보겠습니다.

위와 같이 변경하고, storybook을 실행해 보도록 하겠습니다. 

storybook은 자동으로 변경을 감지하여 재시작이 되므로 따로 재시작 해줄 필요는 없습니다.

![./hanwool_assets/Screen_Shot_2021-04-11_at_10.10.47_PM.png](./hanwool_assets/Screen_Shot_2021-04-11_at_10.10.47_PM.png)

왼쪽에 이름이 Styled Button 이름으로 하나의 버튼이 생긴 것을 알 수 있습니다. 그리고 Canvas에 보면 `Hello` 버튼이 만들어 진 것을 알 수 있습니다.

## StoryBook에 Container 컴포넌트 추가하기

우리가 만든 버튼은 width가 100%로 되어 있기 때문에 화면 전체를 꼭 채우게 됩니다.

그래서 좀 더 보기 편하도록 Storybook 내부에서만 사용할 Container 컴포넌트를 하나 만들고, 감싸 주도록 하겠습니다.

```jsx
// src/components/Button/index.stories.js
...
import styled from "styled-components";
...

const Container = styled.div`
	width: 200px;
`;

export const StyledButton = () => (
	<Container>
		<Button>Hello</Button>
	</Container>
);
```

실행해 보도록 하겠습니다.

![./hanwool_assets/Screen_Shot_2021-04-06_at_4.11.46_PM.png](./hanwool_assets/Screen_Shot_2021-04-06_at_4.11.46_PM.png)

컨테이너 사이즈 내에서 버튼의 사이즈가 조절 된 것을 알 수 있습니다.

## Storybook에 동적으로 props 추가하기

버튼의 `font-size`를 storybook에서 바꿀 수 있도록 코드를 변경 해 보도록 하겠습니다.

우선 button 컴포넌트에서 fontSize를 props로 받을 수 있도록 코드를 수정하도록 하겠습니다.

```jsx
// src/components/Button/index.js
...
// big일 경우, 32px default는 16px
const StyledButton = styled.button`
  font-size: ${(props) => (props.fontSize === "big" ? "32px" : "16px")};
	...
`;

const Button = ({ fontSize, children }) => (
	<StyledButton fontSize={fontSize}>{children}</StyledButton>
);
export default Button;
```

fontSize를 args로 받아와서 Button 컴포넌트에 넘겨주는 과정을 아래와 같이 작성합니다.

```jsx
// src/components/Button/index.stories.js

// 사용할 컴포넌트에 대한 명세
export default {
	...
	// fontSize 값을 storybook에서 받을 수 있도록 설정하기
	argTypes: {
		fontSize: {
			control: {
				type: "radio",
				options: ["big", "small"],
			},
		},
	},
};

...

// storybook에서 설정한 args 옵션을 받아와 Button 컴포넌트에 추가해주기
export const StyledButton = (args) => (
	<Container>
		<Button {...args}>Hello</Button>
	</Container>
);
```

위와 같이 코드를 변경하고 실행해 보도록 하겠습니다.

![./hanwool_assets/Kapture_2021-04-11_at_22.19.57.gif](./hanwool_assets/Kapture_2021-04-11_at_22.19.57.gif)

라디오 버튼 선택에 따라서 버튼의 크기가 커지고 작아지는 것을 볼 수 있습니다.

이렇게 간략하게 Storybook을 사용하는 방법을 알아 보았습니다. 이외에도 add-on을 추가할 수도 있고, Storybook을 설정하는 방법은 무궁무진합니다.  그래서 모든 기능을 미리 다 알아 보기보단 필요할 때마다 기능을 찾아서 쓰는 방법을 추천드립니다.

![./hanwool_assets/Untitled.png](./hanwool_assets/Untitled.png)

이 블로그 글은 Code Pot, 리액트 챌린지의 과제로 작성되었습니다.

Code Pot, 리액트 챌린지가 궁금하다면? ⇒ [링크](https://www.notion.so/Code-Pot-React-2-a299e9ab5e4c4b97ae59028a90db9720) 

## 전체 코드 살펴보기

- 깃허브에서 전체 코드 보기 -> [바로가기](https://github.com/toy-crane/storybook-tutorial)