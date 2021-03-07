# Styled-components

## 왜 CSS-in-JS를 사용할까?

기존에 웹 사이트를 개발할 때는 HTML, CSS, JavaScript를 각각 다른 파일에 두고 작성하는게 일반적이었다

하지만 최근 React나 Vue, Angur와 같은 라이브러리/프레임워크가 유행하면서 웹 개발의 패러다임이 재사용성에 무게를 두면서 컴포넌트 별로 분리하여 개발하는 방식이 주류가 되었다

따라서 HTML, CSS, JavaScript를 각각 따로 작성하는 것이 아닌 여러개의 컴포넌트로 나눈 후에 하나의 JavaScript에 HTML, CSS, JavaScript을 우겨넣고 재사용하는 패턴이 자주 보인다

이 패턴을 위해 CSS를 js안에서 작성할 수 있도록 도움을 주는 CSS-in-JS 라이브러리를 사용한다

### 리액트는?

그 중 React는 이미 jsx를 사용하고 있기 때문에 HTML을 포함하는 형태를 띄고 있는데 여기에 CSS-in-JS 라이브러리 까지 적용하면 js파일 하나에 모두를 담을 수 있게 된다

라이브러리 중 하나인 Styled-components에 대해서 알아보려고 한다 

그 전에 Styled-components에서 사용하는 주요 문법인 Tagged Template Literal에 대해서 간단하게 알아보자

# Tagged Template Literal

자바스크립트를 배우는 사람이라면 Template Literal은 많이들 들어봤을 것이다

`(백틱) 을 이용한 문자열을 표현하는 방법으로 문자열 내부에 자바스크립트 문법이 들어갈 수 있는 편리한 문법인데

이 템플릿 리터럴을 함수의 인자로 넘겨주게 되면 파싱되어 함수의 파라미터로 들어가게 된다

```jsx
const one = '첫 번째';
const two = '두 번째';
const three = '세 번째';

function taggedTemplateLiteral(strArr, first, second, third){
    console.log(strArr); // ["asdfa", "sd", "fasdf", "asdf", raw: Array(4)]
    console.log(first);  // 첫 번째
    console.log(second); // 두 번째
    console.log(third);  // 세 번째
}

taggedTemplateLiteral`asdfa${one}sd${two}fasdf${three}asdf`;
```

만약 파싱할 파라미터가 확장될 가능성이 존재한다면 Rest Parameter로 인자를 받아올 수도 있다

```jsx
const one = '첫 번째';
const two = '두 번째';
const three = '세 번째';

function a(strArr, ...arr){
		console.log(strArr); // ["asdfa", "sd", "fasdf", "asdf", raw: Array(4)]
    console.log(arr); // ["첫 번째", "두 번째", "세 번째"]
}

a`asdfa${one}sd${two}fasdf${three}asdf`;
```

# Styled-components

## 기본 문법

HTML 엘리먼트(HTML 태그)나 컴포넌트를 스타일링할 때 방법이 살짝 다르다

- HTML 태그 스타일링

    ```jsx
    function Box() {
      const StyledBox =  styled.div`
           // in CSS Style    
      `;
      return <StyledBox>박스</StyledBox>;
    }
    ```

    이처럼 styled 라이브러리 안에 html의 모든 태그가 정의되어있기 때문에 바로 접근해서 css를 적용시키면 그 css가 적용된 컴포넌트가 반환된다

- 컴포넌트 스타일링

    ```jsx
    function Box() {
      return <div/>;
    }

    const StyledBox = styled(Box)`
        // in CSS Style
    `;
    ```

    styled가 일반적으로 파라미터를 받을 때는 컴포넌트 자체를 파라미터로 받은 후 동일하게 백틱으로 템플릿 리터를을 파라미터로 받는다

    받은 후에는 위와 동일하게 컴포넌트가 반환된다

## 정적 스타일링

Styled-components를 이용해서 간단한 스타일링을 시도해보자

```jsx
import React from 'react';
import styled from "styled-components";

const StyledTitle = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

const StyledWrapper = styled.section`
    width: 10rem;
    height: 5rem;
    padding: 4em;
    margin: auto;
    border-radius: 10%;
    background: papayawhip;
`;

const Title = ({children}) => {
    
    return (
        <StyledWrapper>
            <StyledTitle>{children}</StyledTitle>
        </StyledWrapper>
    )
};

export default Title;
```

이렇게 만든 타이틀 컴포넌트는 다른 리액트 컴포넌트에서 얼마든지 재사용이 가능한데

Title 컴포넌트를 재사용하는 외부 컴포넌트에서 제목을 얼마든지 유동적으로 사용할 수 있게 만들어주었다

```jsx
import React from 'react';
import Title from "./Title";

const App = () => {
    return (
        <div>
          <Title>Hello World</Title>
        </div>
    );
};

export default App;
```

<img width="318" alt="default" src="https://user-images.githubusercontent.com/45571631/110235073-6e178800-7f71-11eb-910d-ab0a5101137a.png">


## 동적 스타일링

템플릿 리터럴을 사용하는 만큼 문자열 안에 함수도 사용이 가능한데 

이를 이용해서 컴포넌트로 들어오는 props값을 이용한 동적 스타일링이 가능하다

```jsx
import React from 'react';
import styled from "styled-components";

const StyledTitle = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: ${props=> props.color || 'palevioletred'};
`;

const StyledWrapper = styled.section`
    width: 10rem;
    height: 5rem;
    padding: 4em;
    margin: auto;
    border-radius: 10%;
    background: ${props=> props.background || 'papayawhip'};
`;

const Title = ({children, background, color}) => {

    return (
        <StyledWrapper background={background} >
            <StyledTitle color={color}>{children}</StyledTitle>
        </StyledWrapper>
    )
};

export default Title;
```

```jsx
import React from 'react';
import Title from "./Title";

const App = () => {
    return (
        <div>
            <Title color={'white'} background={'black'}>Hello World</Title>
        </div>
    );
};

export default App;
```

<img width="312" alt="b w" src="https://user-images.githubusercontent.com/45571631/110235072-6c4dc480-7f71-11eb-9eb7-242a8104ec0b.png">


사용하기에 좋은 예제는 아니지만 스타일링을 위해 넘겨야할 props가 많아진다면 

위에서 언급한 Rest Parameter를 이용해서 넘겨줄 수도 있다

```jsx
...
const Title = ({children, ...props}) => {

    return (
        <StyledWrapper {...props} >
            <StyledTitle {...props}>{children}</StyledTitle>
        </StyledWrapper>
    )
};

export default Title;
```

<img width="312" alt="b w" src="https://user-images.githubusercontent.com/45571631/110235072-6c4dc480-7f71-11eb-9eb7-242a8104ec0b.png">


스타일을 하나만 동적으로 적용하는 경우에는 위 처럼 하면 되지만 여러 부분의 스타일링을 바꾸어야한다면 속성 하나마다 함수를 사용해서 동적할당을 해주어야한다

이런 일은 매우 귀찮기 때문에 다음과 같이 styled안에 css함수를 이용해서 스타일을 덮어 씌운다

```jsx
const StyledTitle = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
  ${props=>props.change && css`
    font-size: 3rem;
    color: blue;
    text-align: left;
  `}
`;
```

```jsx
import React from 'react';
import Title from "./Title";

const App = () => {
    return (
        <div>
            <Title change>Hello World</Title>
        </div>
    );
};

export default App;
```

<img width="314" alt="change" src="https://user-images.githubusercontent.com/45571631/110235150-e9793980-7f71-11eb-9662-353ee48eb6ea.png">


props로 전달된 change의 값이 참인지 거짓인지에 따라서 css 아래에 있는 스타일을 적용시키기 때문에 

change만 Title의 props로 전달해주었다 (이렇게 생략하면 change={true}와 같이 전달된다(