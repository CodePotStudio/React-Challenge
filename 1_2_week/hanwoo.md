# styled system을 쓰는 이유

## front 개발자가 하면서 겪는 개발 이슈들

- 개발자 별로 일관성 없는 스타일
- 반복되는 UI 컴포넌트
- 디자인과 개발에 소요되는 많은 시간들

이와 같은 문제를 해결하기 위해서는 디자이너와 개발자 간의 컴포넌트 일관성을 유지하는 것이 중요하며, 이는 유저에게도 일관성있는 사용자 경험을 줄 수 있습니다.

## 디자인 시스템

디자인 시스템은 일반적으로 제품 내에서 사용하는 asset, component, 디자인 패턴, UX 가이드라인 같은 것들을 포함한 것을 일컫습니다. 이렇게 됨으로써 중앙화되고, 일관성있게 디자인을 발전 시켜 나갈 수 있습니다. 하지만 개발자의 관점에서는 이와 같이 잘 만들어진 디자인 시스템을 재사용이 가능하고, 쉽게 사용할 수 있도록 코드로 옮기는 것이 그 무엇보다 중요합니다.

아래 내용에서는 어떻게 효율적으로 컴포넌트들을 관리하고, 사용할 수 있는지에 대해 두 가지 라이브러리를 통해서 알아보도록 하겠습니다.

## styled component vs styled system

### styled component에서 props 활용하여 style 변경하기

```jsx
import styled from "styled-components";

const Box = styled.div`
	background-color: orange;
	width: ${(props) => props.width || "200px"};
	padding: 20px;
	margin: 0 auto;
`;

function App() {
	return (
		<div>
			<Box width="300px">Hello</Box>;
		</div>
	);
}

export default App;
```

`styled component`에서는 `width: ${(props) => props.width || "200px"};` 컴포넌트를 생성할 때 위와 같이 앞으로 사용할 `props`를 추가하여 넣어 주어야 합니다.

```jsx
import styled from "styled-components";

const Box = styled.div`
	background-color: orange;
	width: ${(props) => props.width || "200px"};
	padding: ${(props) => props.padding || "20px"};
	margin: 0 auto;
`;
```

만약에 `padding` `props`를 추가하고 싶다면 위와 같이 `padding` 값을 `props`에서 받아 오도록 추가해주어야 합니다.

`Box` 뿐만 아니라 `Button`에서도 `width`, `padding` `props`를 써야 한다면 동일한 코드를 반복적으로 각 컴포넌트마다 넣어 주어야 합니다.

```jsx
const Button = styled.button`
	width: ${(props) => props.width || "200px"};
	padding: ${(props) => props.padding || "20px"};
  ...
`;
```

뿐만 아니라 이외에도 사용하다보면 추가적으로 많은 `props`들이 있을텐데 이 것을 일일이 다 기록하는 것은 `don't repeat yourself` 에 맞지 않습니다.

### styled system에서 props 활용하여 style 변경하기

`styled system`은 정해진 `style props`를 통해서 일관성있는 `component`를 만들 수 있도록 도와주는 라이브러리입니다. 말로는 설명의 한계가 있으니, 아래의 코드를 보며 이해해 보도록 하겠습니다.

```jsx
import styled from "styled-components";
import { space, layout } from "styled-system";

const Box = styled.div`
	background-color: orange;
	margin: 0 auto;
	${space}
	${layout}
`;

function App() {
	return (
		<div>
			<Box width="300px">Hello</Box>
			<Box width="300px" p="30px">
				Hello
			</Box>;
		</div>
	);
}

export default App;
```

`styled-system`에서 `space` 와 `layout` 을 import하고, `Box component`에 해당 값을 추가하였습니다. 게다가 `button` 컴포넌트를 만들더라도 손쉽게 `space` 와 `layout` 을 추가하여 사용할 수 있습니다.

게다가 `space`에는 `margin`, `padding`(margin-top, margin-bottom ....) 에 관한 모든 속성을 포함하고 있습니다. 마찬가지로 layout 역시 `widths`, `heights`, `display` 등등 많은 속성들을 포함하고 있습니다.

그런데 `padding`이나 `margin` 같은 경우는 제품의 일관성을 위해 4단위 혹은 8단위로 많이 쓰는데, 위 코드 같이 `padding`에 `30px`을 준다거나 정해진 규칙에 어긋나는 값을 넣으면 일관성 있는 제품을 만들기가 어렵습니다.

이럴 때는 주로 `theme`를 사용해서 제어를 하곤하는데, `styled compoenent`와 `styled system`에서 `theme`를 통해서 어떻게 관리하는지 알아보도록 하겠습니다.

### theming

`theming` 은 모든 컴포넌트에서 일관적인 스타일을 유지하기 위해서 공유하는 값입니다. 예를 들어 웹페이지에 적용될 `primary`, `secondary color` 값, `font size` 등이 해당된다고 볼 수 있습니다. 즉, 전역적으로 사용할 값들을 모든 컴포넌트에서 공유해서 사용하여 일관성있는 UI를 유지하는 방법이라고 볼 수 있습니다.

### styled-component에서 theming

```jsx
import styled, { ThemeProvider } from "styled-components";

const theme = {
	primary: "mediumseagreen",
};

const Box = styled.div`
	color: ${(props) => props.theme.primary};
`;

function App() {
	return (
		<ThemeProvider theme={theme}>
			<div>
				<Box>Hello</Box>
			</div>
		</ThemeProvider>
	);
}

export default App;
```

우선 `theme` 객체를 하나 만듭니다. 여기에는 `react` 내의 모든 컴포넌트에서 공유할 값들을 추가합니다. 그리고 `Box` 컴포넌트에서 `theme`에서 가져오고 싶은 값들을 `props` 에서 꺼내서 적용할 수 있습니다.

하지만 아까와 마찬가지로 `color: ${(props) => props.theme.primary};` 처럼 항목을 추가할 때마다 코드가 반복되는 문제가 있습니다.

### styled-system에서 theming

`styled-system` 에서는 일관성있는 `theming`을 적용하기 위해서 `theme` 객체를 만드는 포맷이 아래와 같이 정해져 있습니다.

```jsx
{
  breakpoints: ['40em', '52em', '64em'],
  space: [0, 4, 8, 16, 32, 64, 128, 256, 512],
  fonts: {
    body:
      'system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", sans-serif',
    heading: 'inherit',
    monospace: 'Menlo, monospace',
  },
  fontSizes: [12, 14, 16, 20, 24, 32, 48, 64, 96],
  fontWeights: {
    body: 400,
    heading: 700,
    bold: 700,
  },
  lineHeights: {
    body: 1.5,
    heading: 1.125,
  },
  colors: {
    text: '#000',
    background: '#fff',
    primary: '#07c',
    secondary: '#30c',
    muted: '#f6f6f6',
  },
}
```

여기서 `color`만 사용하여 `component`에 `style`을 추가해보겠습니다.

```jsx
import styled, { ThemeProvider } from "styled-components";
import { color } from "styled-system";

const theme = {
	colors: {
		primary: "#07c",
	},
};
const Box = styled.div`
	${color}
`;

function App() {
	return (
		<ThemeProvider theme={theme}>
			<div>
				<Box color="primary">Hello</Box>
			</div>
		</ThemeProvider>
	);
}

export default App;
```

이렇게 손쉽게 `theme`에 있는 값들을 컴포넌트에 넣어 줄 수 있게 됩니다.

맨 처음에 이야기했던 4px 단위의 `padding`도 한 번 넣어보도록 하겠습니다.

```jsx
import styled, { ThemeProvider } from "styled-components";
import { color, space } from "styled-system";

const theme = {
	colors: {
		primary: "#07c",
	},
	space: [0, 4, 8, 16, 32, 64, 128, 256, 512],
};
const Box = styled.div`
	${color}
	${space}
`;

function App() {
	return (
		<ThemeProvider theme={theme}>
			<div>
				<Box color="primary" p={1}>
					Hello
				</Box>
			</div>
		</ThemeProvider>
	);
}

export default App;
```

위 코드 속의 `p={1}` 처럼 `space` 리스트의 인덱스를 값으로 넣어주면 해당하는 값을 `padding`값으로 넣어 줄 수 있습니다. 이를 통해 전체 웹 페이지에 일관성있는 `space`를 관리 할 수 있게 됩니다.

이외에도 미디어 쿼리에 따라 `style`을 손쉽게 변경해 줄 수도 있고, `variant`를 주어 정해진 규칙에 따라 컴포넌트를 손쉽게 스타일을 변경할 수도 있습니다.

자세한 내용이 궁금하시다면 아래의 블로그 글과 document를 읽어 보시는 것을 추천드립니다.

[https://medium.com/javascript-in-plain-english/responsive-react-app-with-styled-system-based-on-design-system-a82c6f9d5e2e](https://medium.com/javascript-in-plain-english/responsive-react-app-with-styled-system-based-on-design-system-a82c6f9d5e2e)

[https://styled-system.com/](https://styled-system.com/)
