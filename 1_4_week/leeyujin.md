## CSS 작성 방법 결정하기

- 돔 요소에 스타일을 적용하기 위해서 CSS를 이용한다.
- CSS를 작성하는 전통적인 방법은 CSS를 별도의 파일로 작성하고 HTML의 link를 이용한다.
- 순수 CSS문법은 코드를 재사용하기 힘들기 때문에 Sass를 이용하기도 한다.
- Sass는 믹스인(mixin) 개념이 있어서 중복 코드를 많이 줄일 수 있다.
- 리액트로 프로그래밍 할 때에는 컴포넌트 중심으로 생각하는 것이 좋다.
- 컴포넌트는 서로 간의 의존성을 최소화하면서 내부적으로 응집도를 높여야 한다.

```
npx create-react-app css-test

```

### 일반적인 CSS파일로 작성하기

- src 폴더 밑에 Button.js.Button.css파일을 만들어보자

```
// Button.css // 1
.big {
    width : 100px;
}
.small {
    width: 50px;
}
.button {
    height: 30px;
    background-color: #aaaaaa;
}

// Button.js
import React from 'react';
import './Button1.css'; // 2

function Button({ size }) {
    if (size === 'big') {
        return <button className="button big">큰 버튼</button>;// 3
    }else{
        return <button className="button small"> 작은 버튼 </button>;
    }
}
export default Button;

```
- 1 : Button.css 파일은 일반적인 css파일이다.
- 2 : CSS 파일도 ESM 문법을 이용해서 자바스크립트로 가져올 수 있다.
- 3 : Button1.css 파일에 정의된 CSS 클래스명을 입력한다.

```
// Box1.css
.big {
    width: 200px;
}
.small {
    width: 100px;
}
.box {
    height: 50px;
    background-color: #aaaaaa;
}

// Box1.js 파일
import React from 'react';
import './Box1.css';

function Box({size}) {
      if (size === 'big') {
        return <button className="button big">큰 버튼</button>;// 3
    }else{
        return <button className="button small"> 작은 버튼 </button>;
    }
}
export default Box;

```