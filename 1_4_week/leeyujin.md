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

```
import React from 'react';
import Button from './Button1';
import Box from './Box1';

export default function App() {
    return (
        <div>
            <Button size="big" />
            <Button size="small" />
            <Box size="big" />
            <Box size="small" />
        </div>
    );
}

```

- npm start를 이용해서 실행하면 화면에 버튼 두 개와 박스 두 개가 보인다.
- Button1.css파일에서 small,big 클래스로 정의한 너비가 제대로 적용되지 않은것을 발견할 수 있다.
- npm run build를 실행해서 build/static/css 폴더 밑에 생성된 CSS파일을 확인해보자.

```
// 1
.big {
    width: 100px;    
}
.small {
    width: 50px;
}

.button {
    height : 30px;
    background-color:#aaa;
}

// 2
.big {
    width: 200px;
}
.small {
    width: 100px;
}
.box {
    height: 50px;
    background-color:#aaa;
}
```

- CSS 클래스명이 같기 때문에 1번 코드는 2번코드로 대체된다.

## CSS-module로 작성하기

- css-module을 사용하면 일반적인 CSS 파일에서 클래스명이 충돌할 수 있는 단점을 극복할 수 있다.
- css-moduled은 간결한 클래스 명을 이용해서 컴포넌트 단위로 스타일을 적용할 때 좋다.
- create-react-app에서는 CSS 파일 이름은 다음과 같이 작성하면 css-module이 된다.

{이름}.module.css

```
import React from 'react';
import style from './Button2.module.css'; // 1

function Button( { size } ) {
    if(size === 'big') {
        return <button className = {`${style.button} ${style.big}`}>큰 버튼 </button> // 2
    } else {
        return {
            <button className = {`${style.button} ${style.small}`}>작은 버튼 </button>
        }
    }
}
export default Button;
console.log{style};// 3
```
- css-module은 클래스명 정보를 담고 있는 객체를 내보낸다.
- CSS 파일에서 정의된 클래스명이 style객체의 속성 이름으로 존재한다.
- style 객체를 출력한 결과는 다음과 같다.