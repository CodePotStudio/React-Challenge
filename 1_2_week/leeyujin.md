# 리액트 환경 직접 구축해보기

- 1.https://unpkg.com/react-dom@16.14.0/umd/react-dom.development.js
- 2.https://unpkg.com/react@16.14.0/umd/react.development.js
- 1 : 개발환경에서 사용하는 파일
- 2 : 배포 환경에서 사용하는 파일이다.
- 보통은 `npm install`을 이용해서 리액트를 설치한다. (바벨이나 웹팩을 사용할 경우)
- `npm install`은 리액트를 이해하는데 방해되므로 이렇게 진행해보는것도 좋다.


# 바벨 사용해 보기

- 자바스크립트 코드를 변환해 주는 컴파일러이다. 
- 바벨을 사용하게 되면 최신 자바스크립트 문법을 지원하지 않는 환경에서도 최신 문법을 사용할 수 있다.
- 바벨은 자바스크립트 최신 문법을 사용하는 용도 외에도 다양하게 활용될 수 있다.
- 리액트에서는 JSX 문법을 사용하기 위해서 바벨을 사용한다.
- 바벨이 JSX문법으로 작성된 코드를 `createElement` 함수를 호출하는 코드로 변환해 준다.


```

// test 01

function LickButton() {
 // 기존 코드와 같다.

}

function Container() {

    // 1
    const [count, setCount] = React.useState(0);
    return React.createElement(
        'div',
        null,
        React.createElement(LikeButton),
        React.createElement,
        'div',
        {style: {marginTop: 20 } },
        React.createElement{'span', null, '현재 카운트: '},
        React.createElement{'span', null, count},
        React.createElement{
            'button',
            { onClick: () => setCount(count + 1) },
            '증가',
    ),
    React.createElement(
        'button',
        {onClick: () => setCount(count - 1) },
        '감소',
        ),
    ),    
);
}
    // 2
const domContainer = document.querySelector('#react-root');
ReactDOM.render(React.createElement(Container), domDontainer);

```
- 1은 단순한 기능임에도 불구하고 코드가 상당히 복잡하다.
- 2 기존에 LikeButton이 들어 있던 코드가 Container로 변경되었다. 그 대신에 LickButton 컴포넌트는 Container 컴포넌트 내부에서 사용되고 있다.

# JSX 문법 사용해 보기
- Container 컴포넌트는 JSX 문법을 사용하게 되면 가독성이 높아지게 됩니다.
- 위 test01 코드를 JSX 문법을 사용한 버전으로써 작성하게 되면 이렇게 된다.

hello-world<br>
|___react.development.js<br>
|___react.production.min.js<br>
|___react-dom.development.js<br>
|___react-dom.production.min.js<br>
|___simple4.html<br>
|___src<br>
    |___simple4.js<br>

- createElement 함수를 호출하는 코드를 JSX 문법으로 변경하면 이렇게 됩니다.

```
function Container() {
    const [const, setCount] = React.useState(0);
    return (
        <div>
            <LikeButton />
            <div style = {{ marginTop: 20 }}>
                <span>현재 카운트 : </span>
                <span>{count}</span>
                <button onClick={() => setCount(count + 1)>증가</button>
                <button onClick={() => setCount(count - 1)>감소</button>
            </div>
        </div>
    );
}
```
# JSX 문법을 알아보기

- JSX는 HTML에서 태그를 사용하는 방식과 유사합니다. createElement 함수를 사용해서 작성하는 것보다는 JSX 문법을 사용하는 게 간결하면서도 가독성도 좋다.
- HTML 태그와의 가장 큰 차이는 속성값을 작성하는 방법에 있다.

```
<div className= "box"> // 1
    <Title text = "hello world" width ={200}/> // 2
    <button onClick{() => {}}>좋아요</button> // 3
    <a href="/home" style ={{ marginTop : '10px', color: 'red'}}> // 4
        홈으로 이동
    </a>
</div>
```

- 1 : HTML에서 돔 요소에 CSS클래스 이름을 부여하게 된다고 하였을때 class 키워드를 사용한다면 JSX에서는 className키워드를 사용한다.(자바스크립트 class와 동일)
- 2 : Title은 리액트 컴포넌트입니다. JSX에서는 돔 요소의 리액트 컴포넌트를 같이 사용할 수 있다. Title 컴포넌트는 text,width라는 두개의 속성 값을 입력받는다.
- 3 : 이벤트 처리 함수는 브라우저마다 다르게 동작할 수 있기 때문에 리액트와 같은 라이브러리를 사용하지 않을 때에는 주의해야 한다.
- 4 : HTML에서 돔 요소에 직접 스타일을 적용하는 것과 같이 JSX에서도 스타일을 적용할 수 있다.  대신 카멜 케이스를 이용한다.

# JSX 문법을 바벨로 컴파일하기 

- JSX 문법은 자바스크립트 표준이 아니기 때문에 에러가 발생한다.
- `simple4.js`파일을 `createElement`함수로 작성된 파일로 변환하기 위해서는 다음 패키지를 설치해야 한다.

`npm install @babel/core @babel/cli @babel/preset-react`

# 바벨 플러그인과 프리셋

- 바벨은 자바스크립트 파일을 입력으로 받아서 또 다른 자바스크립트 파일을 출력으로 준다.
- 자바스크립트 파일을 변환해 주는 작업은 플러그인(plugin) 단위로 이루어진다.
- 두 번의 변환이 필요하다고 한다면 두 개의 플러그인을 사용하게 됩니다.
- 플러그인의 집합을 프리셋(preset)이라고 한다.

# 웹팩의 기본 개념 이해하기

- 웹팩은 자바스크립트로 만든 프로그램을 배포하기 좋은 형태로 묶어주는 도구이다. 
- 예전에는 페이지가 전환될 때마다 새로운 HTML파일을 요청해서 화면을 새로 그렸다. 
- 자바스크립트는 돔을 조작하는 간단한 역할만 했기 때문에 코드의 양이 많지 않았다.
- 최근 웹사이트 제작 방식이 단일 페이지 애플리케이션 (single page application)으로 전환되면서 상황이 달라지게 되었다.
- 파일간의 의존성 떄문에 선언되는 순서를 신경쓰게 되었고 이는 자바스크립트 파일이 앞에 선언된 파일에서 생성된 전역변수를 덮어쓰는 위험도 존재하게 되었다.
- 웹팩은 ESM(ES6의 모듈 시스템)과 commonJS를 모두 지원한다. 이들 모듈 시스템을 이용해서 코드를 작성하고 웹팩을 실행하면 예전 브라우저에서도 동작하는 자바스크립트 코드를 만들 수 있다.

## 자바스크립트의 모듈 시스템

- 자바스크립트에서는 ES6부터 모듈 시스템이 언어 차원에서 지원된다.
- 현재 모든 최신 브라우저에서는 ES6의 모듈 시스템을 지원한다.
- 예전 버전의 브라우저에서는 모듈 시스템을 사용할 수 없다.
- 또한 상당히 많은 수의 오픈 소스가 ES6 모듈로 작성되지 않았다는 것도 큰 걸림돌이다.
- 현재 많은 수의 오픈 소스가 commomJS 모듈 시스템으로 구현되어 있다.

## ESM 문법 익히기

- ESM 문법을 익히기 위해 모듈을 내보내고 가져오는 코드를 만들어 봅시다.

```
// file1.js 파일
export default function func1() {} // 1

// 2
export function func2() P{
export const variable1 = 123;
export let variable2 = 'hello';
}

// file2.js 파일
import myFun1, { fun2, variable1, variable2 } from './file1.js'; // 3

// file3.js 파일
import { func2 as myFunc2 } from './file1.js'// 4
```
- 1,2 코드를 내보낼 때는 export 키워드를 사용한다.
- 3 코드를 사용하는 쪽에서는 import,from 키워드를 사용한다.
- 1 default 키워드는 한 파일에서 한 번만 사용할 수 있다.
- 3 default 키워드로 내보내진 코드는 괄호 없이 사용할 수 있고 이름은 원하는 대로 정할 수 있다.
- 1 번 코드에서 내보낸 func1 함수는 3 번 코드에서 myFun1 이라는 이름으로 가져왔다.
- 3 default 키워드 없이 내보내진 코드는 괄호를 사용해서 가져온다. 가져올 때는 내보낼 때 사용된 이름 그대로 가져와야 한다.
- 원한다면 as 키워드를 이용해서 이름을 변경해서 사용할 수 있다. 

# 웹팩 사용해보기 

- `webpack-test` 라는 폴더를 만들고 그 폴더에서 다음 명령어를 실행한다.

`npm init -y`

- package.json파일이 만들어지면서 simple1.html파일을 복사해서 webpack-test 폴더 밑에 index.html 파일을 만들고 index.html에 있는 simple1.js문자열을 dist/main.js로 변경한다.
- react.development.js,react-dom,development.js 파일을 포함하고 있는 script 태그를 지운다.
- webpack-test폴더 밑에 src 폴더를 만들고 내용이 없는 index.js,Button.js파일을 만든다.

webpack-test<br>
|___package.json<br>
|___index.html<br>
|___src<br>
    |___index.js<br>
    |___Button.js

- 외부 패키지를 설치해 보자

`npm install webpack webpack-cli react react-dom`

 - 웹 팩과 같이 리액트 패키지도 설치한다.
 - 리액트 패키지에는 react,production.min.js,react.development.js 파일이 포함되어 있다.
 - react-dom 패키지에는 react-dom.production.min.js,react-dom.development.js파일이 포함되어 있다.
 - 이로써 모듈 시스템과 npm 덕분에 외부 패키지를 프로젝트에 쉽게 포함할 수 있다.

```
// 1
import React from 'react';
import ReactDOM from 'react-dom';
import Botton from './button.js';

function Container() {
    return React.createElement(
        'div',
        null,
        React.creactElement('p',null,'버튼을 클릭해 주세요.'),
        React.creactElement(Button, { label : '좋아요' }),
        React.creactElement(Button, { label : '싫어요' }),
    );
}
const domContainer = document.querySelector('#react-root');
ReactDOM.render(React.createElement(Container), domContainer);
```
- 1: ESM 문법을 이용해서 필요한 모듈을 가져오고 있다.

## 클래스형 컴포넌트와 함수형 컴포넌트 

- 리액트 컴포넌트는 클래스형 컴포넌트 또는 함수형 컴포넌트로 작성될 수 있다.
- 기능적인 측면에서는 클래스형 컴포넌트는 함수형 컴포넌트가 할 수 있는 모든 일을 할 수 있다.
- 함수형 컴포넌트가 할 수 없는 일은 `상태값을 가질 수 없다,리액트 컴포넌트의 생명주기 함수를 작성할 수 없다.`
- 훅(hook)이라는 기능이 추가가 되면서 함수형 컴포넌트에서는 상태값과 생명주기 합수 코드를 작성할 수 있게 되었다.
- 그러므로 훅과 함께 함수형 컴포넌트를 작성하는 것이 좋다.
