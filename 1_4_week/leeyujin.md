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

```
// css-module로 작성된 CSS파일을 가져온 결과
{
    big : 'Button2_big_iAXxH',
    small: 'Button2_small__1G4lx',
    button: 'Button2_box_D8Lg-',
}
```

```

<style type="text/css">
.Button2_big__1deZX {
    width : 100px;
}
.Button2_small__1G4lx {
    width : 50px;
}
.button2_button__D8Lg- {
    height: 30px;
    background-color: #aaaaaa;
}
</style>

```
- Button2.module.css 파일에서 입력된 내용들이 클래스명만 변경된 채로 들어있습니다.
- 다른 CSS파일에서 같은 이름의 클래스명을 사용하더라도 이름 충돌은 발생하지 않는다.
- Button2.js파일에서는 className에 속성값을 입력하는 코드가 번거롭기도 하면서 가독성이 좋지 않다.
- classnames 패키지를 설정한다.-> npm install classnames

```
// classnames 사용하기
// ...
import sn from 'classnames';
// ...
<button className={cn{style.button,style.big}}>큰 버튼 </button>
//...
<button className={cn{style.button,style.small}}>작은 버튼 </button>
//...

```

```
// css-module로 작성된 box 컴포넌트
import React from 'react';
import style from './Box2.module.css';
import cn from 'classname';

function Box({ size }) {
    const inBig = size === 'big';
    const label = isBig ? '큰 박스' : '작은박스';
    return (
        <div
            className={cn{style.box, { {style.big}: isBig, {style.small}: !isBig }}} // 1
        >
        {label}
        </div>
    );
}
export default Box;
```
- cn 함수의 인수로 객체를 사용하면 조건부로 클래스명을 입력할 수 있다.

### Sass로 작성하기

- CSS와 비슷하지만 별도의 문법을 이용해서 생산성이 높은 스타일 코드를 작성할 수 있게 도와준다.
- Sass 문법에 있는 변수, 믹스인(mixin) 등의 개념을 이용하면 스타일 코드를 재사용할 수 있다.

```
// Sass로 작성된 스타일 코드
$sizeNormal : 100px; // 1

.box {
      width : $sizeNormal; // 2
      height : 80px;
}

.button {
     width : $sizeNormal; // 3
     height : 50px;
}

```

- 1 : 일반적인 프로그래밍 언어처럼 변수를 정의할 수 있다.
- 2~3 : 변수를 사용하면 코드 중복을 없앨 수 있다.
- Sass 문법으로 작성한 파일은 별도의 빌드 단계를 거쳐서 CSS 파일로 변환된다.
- create-react-app에서 Sass를 사용하고 싶다면 아래와 같은 패키지를 설치해보자.

```
npm install node-sass;

```
- node-sass 패키지는 Sass를 CSS로 빌드할 때 사용한다. 
- create-react-app에는 Sass를 위한 빌드 시스템이 구축되어 있다.
- scss 확장자를 가지는 파일을 불러오게 되면 자동으로 Sass 파일이 CSS파일로 컴파일 된다.

```
// Button3.module.scss
@import './shared.scss'; // 1
.big {
    width : 100px;
}
.small {
    width : 50px;
}
.button {
    height : 30px;
    background-color:$infoColor; // 2
}
```
- 1: 자바스크립트의 모듈 시스템과 비슷하게 다른 SCSS 파일을 가져올 수 있다.
- 2: 다른 scss파일에 정의된 변수를 사용할 수 있다.

```
@import './shared.scss'; // 1

.big {
    width : 200px;
}
.small {
    width : 100px;
}
.box {
    height : 50px;
    background-color:$infoColor;
}
```
- 1 : Sass 모듈 시스템 덕분에 스타일 코드를 재사용할 수 있다.
- npm run build를 실행 후 생성된 CSS 파일을 열어보자. shared.scss 파일의 변수가 .box .button 스타일에 적용된 것을 확인할 수 있다.

## css-in-js로 작성하기
- css-in-js는 리액트의 인기에 힘입어서 비교적 최근에 떠오르고 있는 방법이다.
- css 코드를 자바스크립트 파일 안에서 작성한다.
- css 코드가 자바스크립트 안에서 관리가 되기 때문에 공통되는 CSS코드를 변수로 관리할 수 있다.
- css-in-js를 지원하는 패키지가 많이 나왔고 문법도 다양하다.
- css-in-js를 지원하는 패키지 중에서 가장 유명한 style-components를 사용해보자.

```
npm install styled-components

```

```
import React from 'react';
import styled from 'stylee-components';

const BoxCommon = styled.div`// 1
    height: 50px;
    background-color:#aaaaaa;
    ;
const BoxBig = styled{BoxCommon}` // 2
    width: 200px;
    ;
const BoxSmall = styled(BoxCommon)`
    width: 100px;
    ;

    function Box{{ size }} {
        if (size === 'big') {
            return <BigBig>큰 박스</BigBig>;// 3
        } else {
            return <BoxSmall>작은 박스</BoxSmall>;
        }
    }
    export default Box;
```
- 1 : 공통 CSS코드를 담고 있는 style-components 컴포넌트를 만들었다.
- 2 : 클래스 상속처럼 이전에 만든 BoxCommon 컴포넌트를 확장해서 새로운 style-components 컴포넌트를 만들 수 있다.
- 3 : styled-components 컴포넌트는 일반 적인 리액트 컴포넌트처럼 사용할 수 있다.
- 여기서 styled-components를 만들 때 사용되는 문법이 생소할 수 있다. 이는 ES6에 추가가 된 태그된 템플릿 리터럴 문법이다.
- App.js파일에서 Box4.js파일을 가져오도록 수정하고 npm start를 실행하면 의도한 대로 동작하는 것을 확인할 수 있다.

```
import React from 'react';
import styled from 'styled-components';

const BoxCommon = styled.div`
    width: $ {props => {props.inBig ? 200 : 100}}px;// 1
    height : 50px;
    background-color: #aaaaaa;
    ;

    function Box{{ size }} {
        const inBig = size === 'big';
        const label = isBig ? '큰 박스' : '작은 박스';
        return <BoxCommon isBig ={isBig}>{label}</BoxCommon>l// 2
    }
export default Box;
```

- 1 : 템플릿 리터럴에서 표현식을 사용하면 컴포넌트의 속성값을 매개변수로 갖는 함수를 작성할 수 있다. 
- 또한 동적으로 스타일을 변경하기 때문에 styled-components 컴포넌트는 하나로 충분하다.
- 2 : isBig 속성값은 styled-components 컴포넌트의 표현식에서 사용된다.


