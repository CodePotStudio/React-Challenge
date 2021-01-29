# create-react-app으로 시작하기

- create-react-app은 리액트로 웹 애플리케이션을 만들기 위한 환경을 제공합니다.
- create-react-app을 이용하게 된다고 한다면 기존 기능을 개선하거나 또는 새로운 기능을 추가했을 때 패키지 버전을 올리면 된다

# create-react-app 사용해보기 

```
npx create-react-app cra-test

```
- npm 버전이 낮아서 실행이 되지 않는다면 다음과 같이 입력합니다.

```
npm install -g create-react-app
create-react-app cra-test

```

- 웹페이지를 띄워보는 방법은 다음과 같다.

```
cd cra-test
npm start

```
- 빌드가 끝나게 되면 자동으로 브라우저에서 새 탭이 열린다.
- create-react-app에서 자동으로 생성된 파일을 살펴보자면 다음과 같다.

cra-test<br>
|___README.md<br>
|___node_modules<br>
|___package.json<br>
|___public<br>
|   |___favicon.ico<br>
|   |___index.html<br>
|___src<br>
    |___App.css<br>
    |___App.js<br>
    |___App.test.js<br>
    |___index.css<br>
    |___index.js<br>
    |___logo.svg<br>   
    |___serviceWorker.js

- `index.html,index.js` 파일은 빌드시 예약된 파일 이름이기 때문에 지우면 안된다.
- `index.html,index.js,package.json` 파일을 제외한 나머지 파일은 데모 앱을 위한 파일이기 때문에 수정하거나 삭제해도 괜찮다.
- `index.js`로부터 연결되어있는 모든 자바스크립트 파일과 CSS파일은 src 폴더 밑에 있어야 한다. (src 폴더 바깥에 있는 파일은 import 키워드를 이용해서 가져오려고 하면 실패한다.)
- index.html 에서 참조하는 파일은 public 폴더 밑에 있어야 한다. 또 자바스크립트 파일이나 CSS 파일을 link나 script 태그를 이용해 포함할 수 있다.
- src 폴더 밑에서 import키워드를 사용해서 포함시키는 것이 좋다.
- index.html의 내용을 직접 수정해도 괜찮지만 제목을 페이지별로 다르게 줘야 한다고 한다면 문제가 될 수 있다.
- 사내에서 쓰이는 웹사이트라 한다면 react-helmet과 같은 패키지를 사용하면 된다.
- 검색 엔진 최적화가 중요하다고 한다면 서버사이드 렌더링에 특화된 넥스트(next.js)를 사용하는 것이 좋다.
- serviceWorker.js파일에는 PWA(progressive web app)과 관련된 코드가 들어있다.
- PWA는 오프라인에도 잘 동작하는 웹 애플리케이션을 만들기 위한 기술이다. 기본적으로 create-react-app에서는 꺼져 있는 상태이다.
- create-react-app에 PWA기능을 원한다고 한다면 index.js파일에 serviceWorker.register(); 코드를 넣으면 된다.


## 주요 명령어 알아보기

- package.json파일을 열어보게 되면 npm 스크립트 명령어를 확인할 수 있다. 
- `npm start`는 개발모드로 프로그램을 실행하는 명령어이다. 
- 개발모드로 실행하면 HMR이 동작하기 때문에 코드를 수정하면 화면에 즉시 반영된다.
- HMR이 없다면 코드를 수정하고 브라우저에서 수동으로 새로고침을 해야 하므로 번거롭다.
- 에러 메시지가 출력된 영역을 클릭하게 되면 에러가 발생한 파일이 에디터에서 열린다.
- 때에 따라서 API 호출을 위해서 https로 실행해야 할 수 있는데 create-react-app에서는 기본적으로 실행하는 옵션을 제공합니다.

```
맥: HTTPS = true npm start
윈도우 : set HTTPS = true && npm start
```

- 이 명령어를 실행하게 되면 서명된 인증서와 함께 http 사이트로 접속된다.

## 빌드하기

- npm run build 명령어는 배포 환경에서 사용할 파일을 만들어준다.
- 빌드 후 생성된 자바스크립트 파일과 CSS 파일을 열어보면 사람이 읽기 힘든 형식으로 압축된 것을 확인할 수 있다.

```
npx serve -s build

```

- serve 패키지는 노드 (node.js) 환경에서 동작하는 웹 서버 애플리케이션이다.
- `build/static` 폴더 밑에 생성된 파일의 이름에 해시값이 포함되어 있다.
- 파일의 내용이 변경되지 않으면 해시값은 항상 같다.
- 새로 빌드를 하더라도 변경되지 않은 파일은 브라우저에 캐싱되어 있는 파일들이 사용된다.
- 재방문의 경우 빠르게 페이지가 렌더링 되는 효과를 볼 수 있다.
- 자바스크립트 파일에서 import 키워드를 이용해서 가져온 CSS 파일은 다음 경로에 저장합니다.


```

bulid/static/css/main.{해시값}.chunk.css

```

- 여러개의 CSS 파일을 임포트하더라도 모두 앞의 파일에 저장된다.
- 자바스크립트 파일에서 import 키워드를 이용해서 가져온 폰트,이미지 등의 리소스 파일들은 bulid/static/media 폴더 밑에 저장된다.

```
//...
// 1
import smallImage from './small.jpeg';
import bigImage from './big.jpeg';

function App() {
    return (
        <div className-"App">
        // 2
            <img src={bigImage} alt ="big" />
            <img src={smallImage} alt ="small" />
        // ...
    )
}

```
- 1 : 일반적인 자바스크립트 모듈처럼 이미지 파일을 자바스크립트로 가져오게 됩니다.
- 2 : bigImage,smallImage 변수는 해당 이미지의 경로를 나타내는 문자열이다.
- media 폴더에는 `big.{해시값}.jpeg` 파일이 생성된다.
- `small.{해시값}.jpeg` 파일은 생성되지 않는다. 해당 파일은 main.{해시값}.js 파일에서 data:image 키워드를 검색해본다.
- 이미지 파일이 문자열 형태로 자바스크립트 파일에 포함되어 있다.

# 테스트 코드 실행하기

- `npm test` 를 입력하게 되면 테스트 코드가 실행하게 됩니다.
- create-react-app에는 제스트(jest)라는 테스트 프레임워크를 기반으로 테스트 시스템이 구축되어 있다.
- create-react-app으로 프로젝트를 생성하게 되면 `App.test.js`파일이 생성된다.
- __tests__ 폴더 밑에 있는 모든 자바스크립트 파일
- 파일 이름이 .test.js로 끝나는 파일
- 파일 이름이 .spec.js로 끝나는 파일

```
export function addNumber(a,b) {
    return a; // 1
}

```

- 1: 코드에 버그가 있기 때문에 테스트 코드를 작성하면 실패할 것이다.
- util.test.js파일을 생성해서 addNumber 함수를 테스트하는 코드를 작성하면 이렇게 된다.

```
import { addNumber } from './util';

it('add two numbers', () => { // 1
    const result = addNumber(1,2);
    expect(result).tobe(3); // 2
})

```
- 1,2 : it,expect는 제스트에서 테스트 코드를 작성할 때 사용하는 함수이다.
- CI(Continuous Integration)은 개발자를 위한 자동화 프로세스인 지속적인 통합을 의미합니다.
- CI와 같이 watch모드가 필요 없는 환경에서는 다음과 같이 테스트 코드를 실행한다.

- 맥 : CI = true npm test
- 윈도우 : set "CI = true" && npm test

## 설정 파일 추출하기

- npm run eject를 실행하게 되면 숨겨져 있는 create-react-app 의 내부 설정 파일이 밖으로 노출된다.
- 이 기능을 사용하게 되면 바벨이나 웹팩의 설정을 변경할 수 있다.
- 이 기능의 단점이라고 한다면 create-react-app에서 개선하거나 추가된 기능이 단순히 패키지 버전을 올리는 식으로 적용되지 않는다는 점이다. (리액트 툴체인에 익숙한 사람이 아니라면 추천하지 않는다.)
- create-react-app 설정을 변경하는 방법은 다음과 같다.
- 방법 1 : react-scripts 프로젝트를 포크해서 나만의 스크립트를 만든다.  
- 방법 2 : react-app-rewired 패키지를 사용한다.     
- 방법 1 의 경우에는 자유도가 높기 때문에 원하는 부분을 수정할 수 있다.
- 방법 2 는 자유도가 낮지만 비교적 쉽게 설정을 변경할 수 있다는 장점이 있다.
- 두 방법 모두 create-react-app의 이후 버전에 변경된 내용을 쉽게 적용할 수 없다는 단점이 있다.

## 자바스크립트 지원 범위

- create-react-app에서는 ES6의 모든 기능을 지원한다. (이후에 추가된 기능은 다음과 같다.)

```
- 지수 연산자 
- 나머지 연산자, 전개 연산자
- 동적 임포트
- 클래스 필드
- JSX 문법
- 타입스크립트,플로(flow)타입의 시스템

```
- create-react-app에서는 타입스크립트와 플로 타입 시스템을 지원한다.
- create-react-app의 기본 설정에서는 폴리필(polyfill)도 포함되지 않는다.
- ES6+에서 추가된 객체나 함수를 사용하고 싶다면 직접 폴리필을 넣도록 한다.

```
npm install core-js
// core-js를 설치해보기

```

```

// index.js
import 'core-js/features/string/pad-start'; // 1

//someFile.js
const value = '123'.padStart(5,'0'); // '00123'

```

- 1 : index.js 파일에서 한 번만 가져오면 모든 곳에서 자유롭게 사용할 수 있다.
- 바벨에서도 @babel/polyfill 혹은 @babel/preset-env 프리셋을 이용하면 폴리필을 추가할 수 있다.
- @babel/polyfill은 사용하지 않는 기능의 폴리필까지 모두 포함된다. 그러므로 번들의 크기가 커진다는 단점이 있다.
- @babel/preset-env 프리셋을 이용하면 필요한 폴리필만 추가할 수 있다.
- core-js로 직접 추가하는 것보다는 몇 가지 불필요한 폴리필이 포함된다는 단점이 있다.

## 폴리필

- 새로운 자바스크립트 표준이 나와도 대다수 사용자의 브라우저에서 지원하지 않으면 사용할 수 없다.
- 언어 표준에서는 새로운 문법도 추가가 되고 객체나 함수도 추가가 된다.
- 이러한 새로운 문법은 대부분의 브라우저에서 지원을 하지 않더라도 바벨을 이용하게 되면 어느 정도 사용이 가능하다.
- 기능이 존재하는지 검사해서 그 기능이 없을 때만 주입하는 것을 폴리필이라 한다.

## 코드 분할하기

- 코드 분할을 이용하면 사용자에게 필요한 양의 코드만 내려줄 수 있다.
- 이를 사용하지 않으면 전체 코드를 한 번에 내려주기 때문에 첫 페이지가 뜨는 시간이 오래 걸린다.
- 코드를 분할하는 한 가지 방법은 이전에 언급했던 동적 임포트를 이용하는 것이다. 

```
// Todo.js

import React from 'react';

export function Todo({ title }) {
    return <div>{title}</div>;
}

```

```
// TodoList.js

import React, { useState } from 'react';

export default function TodoList() {
    const [todos, setTodos] = useStat([]); // 1
    const onClick = () => { // 2
        import('./Todo.js').then({ Todo }) => { // 3
            const position = todos.length + 1;
            const newTodo = <Todo key  = {position} title = {`할 일 ${position}`} />; // 4
            setTodos([...todos, newTodo]]);
        });
    };
    return (
        <div>
            <button onClick ={onClick}>할 일 추가</button>
            {todos}// 5
        </div>
    );

}

```
- 1 : 할 일 목록을 관리할 상탯값을 정의한다.
- 2 : 할 일 추가  버튼을 클릭하면 호출되는 이벤트 처리 함수다.
- 3 :  onClick 함수가 호출되면 비동기로 Todo 모듈을 가져온다. 동적 임포트는 프로미스를 반환하기 때문에 then 메서드를 이용해서 이후 동작을 정의할 수 있다.
- 4 : 비동기로 가져온 Todo 컴포넌트를 이용해서 새로운 할일을 만든다.
- 5 : 상탯값에 저장된 할 일 목록을 모두 출력한다.

```
// App.js 파일에서 TodoList 컴포넌트 사용하기

//...

import TodoList from './TodoList';

function App() {
    return (
        <div className="App">
            <TodoList />
            //...
    )
}

```
- 실행을 하게 되면 할일 추가 버튼을 클릭했을 때 1회를 클릭하면 {숫자}.chunk.js파일을 받아오는 것을 확인할 수 있다.
- 화면에는 할 일 목록이 하나 추가된다. 이후 버튼을 클릭하게 되면 더 이상 파일을 받아오지 않고 할 일 목록은 계속해서 추가된다.
- build/static/js 폴더 밑에 {숫자}.{해시값}.chunk.js파일이 추가된것을 확인할 수 있다.
- {숫자}.{해시값}.chunk.js 파일에는 Todo.js 파일의 내용이 들어있다.
- Todo.js파일은 별도의 자바스크립트 파일로 분리되었고 필요한 경우에만 내려받도록 구현된다.
- 단일 페이지 애플리케이션을 만들기 위해서 react-router-dom 패키지를 이용하는 경우에는 react-router-dom에서 지원하는 기능을 이용해 페이지 단위로 코드 분할을 적용할 수 있다.

## 환경 변수 사용하기
- create-react-app에서는 빌드 시점에서 환경 변수를 코드로 전달할 수 있다.
- 환경변수의 경우 개발,테스트,배포 환경별로 다른 값을 적용할 때 유용하다.
- 코드에서 `process.env.{환경 변수 이름}`으로 접근할 수 있다.

### NODE_ENV 환경 변수 이용하기
- create-react-app에서는 NODE_ENV 환경 변수를 기본으로 제공한다.
- npm start로 실행하면 development
- npm test로 실행하면 test
- npm run build로 실행하면 production

```

// ...
console.log(`NODE_ENV= ${process.env.NODE_ENV}`);
// ...

```
- npm start를 실행하면 development가 출력되는 것을 확인할 수 있다.
- `process.env.{환경 변수 이름}`부분은 빌드 시점에 환경 변수값으로 대체된다.
- npm run build를 실행 후 출력되는 자바스크립트 파일을 열어보면 이러한 코드를 살펴볼 수 있다.

```
console.log("NODE_ENV = ".concat("production"))

```

### 기타 환경 변수 이용하기

- NODE_ENV 환경변수 이외에 다른 환경 변수는 REACT_APP 접두사를 붙여야 한다.
- process.env.REACT_APP_ 형태로 접근할 수 있다.
- 맥: REACT_APP_API_URL= api.myapp.com npm start
- 윈도우 : set "REACT_APP_API_URL= api.myapp.com" && npm start
- 환경 변수가 많아지면 .env 파일을 이용하는 것이 좋다.

```

// .env.development 파일
REACT_APP_DATA_API = dev-api.myapp.com
REACT_APP_LOGIN_API = div-auth.myapp.com

// .env.test 파일
REACT_APP_DATA_API=test-api.myapp.com
REACT_APP_LOGIN_API= test-auth.myapp.com

// .env.production 파일
REACT_APP_DATA_API = api.myapp.com
REACT_APP_LOGIN_API = auth.myapp.com

```

## 환경별로 변수가 다르게 출력되는지 확인해보기

```
//...
console.log(`REACT_APP_DATA_API = ${process.env.REACT_APP_DATA_API}`);
console.log(`REACT_APP_LOGIN_API = ${process.env.REACT_APP_LOGIN_API}`);
//...
```

- npm start를 실행하면 .env.development 파일의 내용이 출력되는 것을 확인할 수 있다.
- .env파일에서 로컬 머신에 저장되어진 환경 변수를 이용할 수 있다.
- npm 버전이 로컬 머신의 npm_version 환경 변수에 저장되어 있다고 한다면 다음과 같이 사용할 수 있다.

```

REACT_APP_NODE_VERSION=$npm_version

```

## autoprefixer

- css에서 비교적 최신 기능을 사용하기 위해서는 밴더 접두사(venderprefix)가 붙은 이름을 사용해야 한다.
- create-react-app에서 최신 CSS기능을 사용했을 경우 실제로 벤더 접두사가 자동으로 붙는지 확인해본다.
- src 폴더 밑에 test.css파일을 만들고 App.js에서 test.css파일을 가져오면 된다.

```
//test.css 파일의 내용
.prefix-example {
    writing-mode:horizontal-tb;
    scroll-snap-type: y mandatory;
}

// App.js 파일의 내용
import './test.css';
```

- npm run build를 실행하고 build/static/css 폴더 밑에 생성되는 css파일을 열어보면 다음과 같다.

```
.prefix-example {
    -webkit-writing-mode: horisontal-tb;
    -mb-writing-mode: lr-tb;
    writing-mode: horizontal-tb;
    -webkit-scroll-snap-type: y mandatory;
    -ms-scroll-snap-type: y mandatory;
    scroll-snap-type: y mandatory;
}

// 처음에 입력했던 CSS 속성 외에도 다른 속성이 자동으로 추가된것을 알 수 있다.
```
