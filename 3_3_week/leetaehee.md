# 1. React Component Styling
React의 컴포넌트에 스타일을 적용하기 위해서는 다양한 방법을 사용할 수 있습니다. 가장 기본적인 방법으로 CSS 파일을 `import`해서 사용할 수 있지만 React의 스타일링을 보조해주는 여러 도구와 라이브러리들을 통해 더 편리하게 컴포넌트 스타일링을 적용할 수 있습니다. 

## 1-1. Sass(SCSS)
Sass는 CSS의 단점을 보완하고 선택자의 중첩이나 조건문, 반복문, 다양한 단위의 연산을 통해 CSS를 더 편리하게 사용할 수 있게 해주는 CSS의 전처리기(pre-processor)입니다. Sass는 들여쓰기를 핵심 특성으로 갖는 문법과 중괄호,세미콜론과 같이 CSS와 유사한 특성을 갖는 문법(SCSS) 총 두가지 다른 문법을 지원하고 있습니다. Sass 문법 사용방법은 다음 [링크](https://sass-lang.com/documentation/syntax)에서 확인할 수 있습니다.

### `node-sass` 라이브러리
Sass를 사용하기 위해서는 Sass를 CSS로 변환해주는 `node-sass` 라이브러리가 반드시 필요합니다.
```
$ npm add node-sass
```
### `classnames` 라이브러리
classnames 라이브러리는 컴포넌트에 동적으로 클래스를 부여할 때 사용할 수 있는 라이브러리입니다. 또한 classnames 라이브러리를 사용하면 함수 인자에 문자열, 배열, 객체 등을 전달하여 조건부 스타일링을 손쉽게 할 수 있습니다.
```
$ npm add classnames
```

#### `classnames` 사용 전
```
<button className={["Button", size].join(" ")}>{children}</button>
```
#### `classnames` 사용 후
```
import classNames from "classnames";

<button className={classNames("Button", size)}}>{children}</button>
```

### Sass 스타일 적용하기

&lt;Button.js&gt;
```
import './Button.scss';

function Button({children, size, color}){
    
    // Button, size값(large), color값(blue)으로 된 클래스 적용 -> 동적 스타일 적용

    return <button classNames("Button", size, color)>{children}</button>;
}
```

&lt;App.js&gt;
```
import Button from './Button";

function Button(){
    return <Button size="large" color="blue">Press Button</Button>;
}
```
## 1-2. CSS Module
CSS Module은 React 컴포넌트 이름에 대해 고유한 스타일 클래스명을 만들 수 있는 기법입니다. 이 기법을 사용하면 다른 컴포넌트의 CSS 스타일 클래스명과 중복되지 않기 때문에 CSS 클래스 네이밍 규칙으로 사용할 수 있습니다.

CSS Module을 사용하기 위해서는 별도의 라이브러리의 설치 없이 CSS 파일 확장자를 *.module.css로 설정하기만 하면 사용할 수 있습니다.

## 1-3. Styled-Component
styled-component는 React 내에서 CSS 스타일을 선언할 수 있는 기법(CSS in JS)을 지원해주는 라이브러리입니다. CSS in JS 기법을 사용하면 별도의 CSS 파일을 작성하지 않아도 컴포넌트에 CSS 스타일을 쉽게 적용할 수 있고 `props`값을 사용하여 CSS 스타일을 선언할 수 있습니다.

### `styled-component` 라이브러리 설치
```
$ npm add styled-components
```

### `styled-component` 사용하기
&lt;App.js&gt;
```
import styled, { css } from 'styled-components';

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: ${props.color};
  border-radius: 50%;

  ${props =>
    props.huge &&
    css`
      width: 10rem;
      height: 10rem;
    `}
`;

function App() {
  return <Circle color="red" huge />;
}
```
> `styled-component`의 `css`를 사용하면 여러 줄의 CSS 코드를 조건부로 사용할 수 있습니다. 이를 확장하면 스타일 선언 시 사용한 `props`를 사용한 속성 내부에서도 `props`값을 사용할 수 있습니다.
