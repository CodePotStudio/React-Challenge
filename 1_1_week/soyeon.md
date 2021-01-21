> 이 글은 이재승님의 [실전 리액트 프로그래밍](https://www.inflearn.com/course/%EC%8B%A4%EC%A0%84-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/) 강좌를 듣고 공부하면서 필요한 부분을 정리한 글입니다.  
> [개인 블로그](https://jess2.xyz/react/react-tip-1/) 에도 같은 내용을 작성했습니다.

# 0. Index
1. [리액트란 무엇인가](#1-리액트란-무엇인가)
2. [create-react-app (CRA) 으로 시작하기](#2-create-react-app-cra-으로-시작하기)
3. [CSS 작성 방법 결정하기](#3-css-작성-방법-결정하기)
4. [단일 페이지 어플리케이션 (SPA)](#4-단일-페이지-어플리케이션-spa)

<br>

# 1. 리액트란 무엇인가
### 1-1. 리액트 소개
- 리액트는 페이스북에서 개발하고 관리하는 UI 라이브러리
- 앵귤러가 웹 애플리케이션 개발에 필요한 다수의 기능을 제공하는 것과는 달리 리액트는 UI 기능만 제공한다.
따라서 전역 상태 관리나 라우팅, 빌드 시스템을 각 개발자가 직접 구축해야 한다.
리액트 팀에서 `create-react-app` 이라는 툴을 제공하여 직접 구축해야 하는 번거로움을 줄여주고 있다.

### 1-2. 가상돔
- 리액트는 가상 돔을 통해서 UI를 빠르게 업데이트한다.
- 가상 돔은 이전 UI 상태를 메모리에 유지해서 변경된 부분만 실제 돔에 반영 해주는 기술이다.
- 불필요한 업데이트를 줄여서 성능이 좋아진다.

### 1-3. 리액트와 같은 프레임워크나 라이브러리를 사용하는 가장 큰 이유
- UI를 자동으로 업데이트 해주기 때문
- 원래는 dom을 직접 수정해야 하는데 쉽지 않다.

<br>

# 2. create-react-app (CRA) 으로 시작하기
### 2-1. CRA
- CRA는 리액트 개발환경을 구축해주는 도구.
- React 개발 환경을 직접 구축하려면 많은 지식과 노력이 필요하다. (webpack, babel, jest, eslint, polyfill, HMR, CSS 후처리 등) CRA는 이것들을 모두 자동으로 구축해서 제공해준다.
- 초기에 프로젝트를 구축할 때, **CRA를 사용할 수도 있고, Next.js 라는 프레임워크를 사용할 수도 있다.** 서버사이드 렌더링(SSR)의 지원 여부가 차이다.
- CRA는 서버사이드 렌더링을 지원하지 않는다. **서버사이드 렌더링이 필요한 프로젝트라면 Next.js 를 이용하여 구축하는 것이 좋다.**
- 백오피스와 같이 서버사이드 렌더링 필요 없을 때 CRA를 이용하는 것이 적합하다.

<br>

### 2-2. 프로젝트 만들기
```bash
$ npx create-react-app {PROJECT NAME}
```

<br>

### 2-3. scripts 명령어 종류
- package.json

    ```json
    "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject"
    },
    ```

    - **start**
      - 개발모드로 실행할 때 사용. 따라서 여러가지 최적화가 되지 않은 상태로 실행이 되기 때문에 배포할 때 사용하면 안된다.
      - http로 실행 된다.
      - 만약에 https로 실행하고 싶을 때는 `HTTPS=true npm start` 명령어 입력하면 된다.
    - **build**
      - 배포할 때 사용하는 명령어.
      - 빌드를 하게 되면 정적 파일이 생성된다.
      - 서버에서는 이렇게 생성된 build 폴더 안에 있는 내용들을 정적 파일로 서비스만 하면 된다.
      - 별도로 서버에 애플리케이션을 실행을 하지 않기 때문에 서버사이드 렌더링으로 동작할 수 없는 것이다.
      - 로컬에서 정적 파일을 서비스 하기 위해서 serve 패키지를 사용하고 build 폴더를 대상으로 정적 파일을 서비스한다. react developer tools 크롬 익스텐션을 이용해서 실행된 페이지가 배포용 파일로 실행 된 것이라는 것을 알 수 있다.
      `npx serve -s build`
    - **test**
      - `npm test` : 테스트 실행 명령어
      - 기본적으로 `App.test.js` 파일이 있는데 이 파일 명을 `App.spec.js` 로 변경해도 테스트가 잘 된다.
      - ``__tests__`` 라는 폴더를 만들면 이 폴더 밑에 있는 모든 파일이 테스트 파일이 된다.
      - 하지만 폴더로 관리하는 것 보다는 `.test.js` 라는 이름으로 관리하는 게 좋다고 생각하는데, 그 이유는 **테스트 하려는 파일이랑 붙어 있으면 여러모로 관리하기가 편리하기 때문.**
    - **eject**
      - react-scripts를 사용하지 않고 모든 설정 파일을 추출하는 명령어다.
      - CRA를 기반으로 직접 개발 환경을 구축하고 싶을 때 사용
      - 추출하지 않으면 CRA의 기능이 추가됐을 때 단순히 react-scripts 버전만 올리면 되는데 추출을 하면 수동으로 설정 파일을 수정해야 하는 단점이 있다. 따라서 꼭 필요한 경우가 아니라면 관리를 위해서 추출을 하지 않는 것을 추천.

<br>

### 2-4. build 폴더
- css
  - 여러 개의 css 파일이 하나로 합쳐짐.
  - 애니메이션 속성 : 컴파일된 결과에는 벤더 접두사가 붙은 코드까지 추가가 된다. 그래서 하위 브라우저까지 잘 지원된다.
- js
  - 동적 import 를 사용한 경우에는 별도의 js 파일로 만들어진다.
- media
  - 이미지 파일들이 있다. 이미지 파일은 한 가지 특이한 점이 있는데 이미지 파일의 크기에 따라서 동작이 조금 다르다.
      - 큰 사이즈의 이미지 : media 폴더 밑에 생성된다.
      - 작은 사이즈의 이미지 : 별도의 파일로 생성되는 게 아니라 js 파일 안에 내장이 된다.
      - 그 이유는 http 요청 횟수를 줄이는 그런 목적이 있다. 그렇지만 http 2.0부터는 요청 횟수가 많아도 성능에 영향이 크지 않기 때문에 큰 의미는 없을 것 같다.
      - 또 한가지 이유는 좀 더 빠르게 이미지를 보여줄 수 있다는 그런 이유가 있다. 자바스크립트가 실행되면 바로 이미지를 보여줄 수 있다. JS를 실행한 후에 또 다시 요청을 한 후에 이미지를 가져오는 것보다 JS가 실행될 때 바로 이미지를 보여주는 것이 더 빠르다.

<br>

### 2-5. polyfill
- Internet Explorer 에서 지원하지 않는 기능을 사용하고 싶다면 polyfill을 추가해야 한다.
- 예를 들어서 `padStart`를 사용하고 싶다고 할 때 오래된 브라우저에서는 지원을 하지 않을 수 있다.
브라우저별 지원 여부는 [Can I use](https://caniuse.com/) 에서 확인할 수 있다.
- 보통 core-js를 많이 사용한다. (바벨도 core-js를 사용한다.)
- index.js에서 import해서 포함. `import 'core-js/features/string/pad-start';`
- 원래는 core-js를 설치해서 사용해야겠지만, **CRA에는 기본적으로 core-js가 내장되어 있기 때문에 그냥 import 만 해서 사용하면 된다.**

<br>

### 2-6. 환경 변수

- 개발, 테스트 또는 배포 환경 별로 다른 값을 적용할 때 유용하다.
- 전달된 환경 변수는 코드에서 `process.env.{변수 이름}` 이런식으로 사용할 수 있다.
- CRA에서는 기본적으로 `NODE_ENV` 라는 환경 변수를 갖고 있다. `process.env.NODE_ENV`
    - npm start로 실행하면 `development`
    - npm test로 실행하면 `test`
    - npm run build로 실행하면 `production`
- 환경 변수가 많아지면 .env 파일이라는 것으로 관리하는 것이 좋다.
    - root 경로에 .env.development 파일 생성
    ```text
    REACT_APP_API_URL=api.myapp.com
    REACT_APP_TEMP1=temp_dev1
    ```
    - root 경로에 .env.production 파일 생성
    ```text
    REACT_APP_API_URL=prod.myapp.com
    REACT_APP_TEMP1=temp_prod1
    ```
- 주의 : 리액트 환경 변수는 반드시 `REACT_APP_`로 시작되어야 한다.

<br>

# 3. CSS 작성 방법 결정하기
### 3-1. 일반적인 CSS 파일로 작성하기
- Button.css 파일과 Box.css 파일이 있고 각각의 파일에 **같은 클래스명**에 대한 스타일 정의가 되어 있다면, 원하는 결과가 나오지 않는다. 이것은 **이름이 충돌**해서 발생하는 것.
- 빌드를 해서 css 파일을 한 번 살펴보자. **모든 css 파일이 하나의 빌드 파일**로 합쳐지기 때문에 이름이 충돌되고 나중에 선언된 속성이 적용되는 것이다.
- 일반적인 css 파일로 작성할 때는 이름이 충돌할 수 있는 문제가 있다.

<br>

### 3-2. css-module로 작성하기
- css-module을 사용하면 이름 충돌 문제를 해결할 수 있다.
- Box.module.css, Button.module.css 파일을 만든다.
- import 할 때는 객체 형식으로 내보내기 때문에 **객체 형식으로 받아서 클래스명을 속성 이름으로 입력**해준다.
    ```jsx
    import Style from './Button.module.css';
  
    <button className={`${Style.button} ${Style.big}`)>큰 버튼</button>
    ```

- Style 객체의 각각의 속성 값을 보면 뒤에 **해시 값**이 붙는다. 이 해시 값 덕분에 각 클래스 명은 고유한 이름을 부여 받게 된다. 따라서 이름 충돌 문제도 없게 된다.

- `classnames` 라는 모듈을 이용하면 더 간편하게 입력할 수 있다. (`npm i classnames`로 설치)

    ```jsx
    <button className={cn(Style.Button, Style.big)}>버튼</button>

    <button
        className={cn(Style.Button, {
            [Style.big]: isBig,
            [Style.small]: !isBig,
        })}
    >
        {isBig ? '큰 버튼' : '작은 버튼'}
    </button>
    ```

<br>

### 3-3. Sass로 작성하기
- css와 비슷하지만 **별도의 문법**을 이용해서 생산성이 높은 스타일 코드를 작성할 수 있게 도와준다.
- Sass 문법에 있는 변수나 믹스인 등의 개념을 이용하면 스타일 코드를 재사용 할 수 있다.
- CRA에서 Sass를 이용하려면 필요한 패키지를 설치해야 한다.  
(`npm i node-sass`로 모듈 설치)
- Box.module.scss 와 같은 파일을 만들어서 **sass와 css-module을 같이 사용할 수 있다.**

<br>

### 3-4. css-in-js로 작성하기
> 비교적 최근에 많이 사용하고 있는 방법

- css 코드를 js 파일 안에서 작성하는 방식이다.
- css 코드가 js 파일 안에서 관리되기 때문에 css 코드도 js 코드처럼 얼마든지 재사용 가능한 구조로 관리할 수 있다.
- 또 동적으로 css 코드로 작성하기 쉽다. 자바스크립트로 다 관리할 수 있기 때문.
- 팀에 있는 개발자 대부분이 js와 css 모두 작성할 줄 안다면 css-in-js는 좋은 선택이 될 수 있다.
- 하지만 마크업 개발자가 별도로 있다면 힘들 수 있다고 생각한다.
- 다양한 css-in-js 라이브러리 중에서 `styled-components` 를 사용하는 방법을 알아보자.

#### styled-components

- 모듈 설치
      ```bash
      $ npm i styled-components     
      ```
      
- Example 1

    ```jsx
    import React from 'react';
    import styled from 'styled-components';

    const BoxCommon = styled.div`
        height: 50px;
        backgrouhnd-color: #aaaaaa;
    `;

    const BoxBig = styled(BoxCommon)`
        width: 200px;
    `

    const BoxSmall = styled(BoxCommon)`
        width: 100px;
    `

    export default function Box({ size }) {
        if (size === 'big') {
            return <BoxBig>큰 박스</BoxBig>;
        } else {
            return <BoxSmall>작은 박스</BoxSmall>;
        }
    }
    ```

    - styled-components 에서 styled 라는 객체를 받아온 다음에 .div 로 스타일 코드를 입력하고 있다.
    - `height: 50px; backgrouhnd-color: #aaaaaa;` 를 매개변수로 받아서 실행하는 함수가 있다고 생각하면 된다.
    - `BoxCommon`은 `div`로 이루어진 컴포넌트를 생성한 것이고 이것을 확장해서 또 다른 컴포넌트인 `BoxBig`과 `BoxSmall`을 만들고 있다.
    
- Example 2
    ```jsx
    ...
    const BoxCommon = style.button`
        width: ${props => (props.isBig ? 100 : 50)}px;
        height: 30px;
        background-color: yellow;
    `;

    export default function Box({ size }) {
        const isBig = size === 'big';
        const label = isBig ? '큰 버튼' : '작은 버튼';
        return <BoxCommon isBig={isBig}>{label}</BoxCommon>;
    }
    ```
    - Box 컴포넌트에서 BoxCommon 컴포넌트를 사용할 때, `isBig`이라는 속성값을 넣어주면 BoxCommon 컴포넌트에서 그 속성값을 받아서 동적으로 처리할 수 있다.

<br>

# 4. 단일 페이지 어플리케이션 (SPA)
### 4-1. 멀티 페이지 애플리케이션(전통적인 방식) vs 단일 페이지 애플리케이션
- 멀티 페이지 애플리케이션(MPA)
    - 클라이언트가 초기 요청을 보내면 서버는 html을 내려준다.
    - 이후에 페이지 전환 요청이 있으면 다시 서버로 요청을 보내고 서버는 또 다시 html을 내려준다.
    - 그리고 클라이언트에서는 페이지를 리로드 한다.
- SPA
    - 클라이언트가 초기 요청을 보내면 서버가 html을 내려주는 것 까지는 똑같다.
    - 그리고 이후에 페이지 전환 요청이 있으면 서버로 항상 요청을 하는 것이 아니고 필요할 때만 데이터를 요청해서 받아오는 방식이다.
    - 페이지 전환은 자체적으로 라우팅 처리를 할 수가 있다.

<br>

### 4-2. SPA가 가능하기 위한 조건
- 자바스크립트에서 브라우저로 페이지 전환 요청을 보낼 수가 있어야 한다. 단 브라우저는 서버로 요청을 보내지 않아야 한다.
- 브라우저의 뒤로 가기와 같은 사용자의 페이지 요청을 자바스크립트에서 처리할 수 있어야 한다. 이 때도 브라우저는 서버로 요청을 보내지 않아야 한다.
- 위 조건을 만족시켜주는 브라우저 API
    - pushState, replaceState 함수
    - popState 이벤트

<br>

### 4-3. react-router-dom
- 리액트에서는 이 npm package를 이용해서 라우팅 처리를 할 수가 있다.
- 페이지별 코드 스플리팅 기능이 제공된다.
- example
    ```jsx
    import React from 'react';
    import { BrowserRouter, Router, Link } from 'react-router-dom';
    ...
    
    export default function App() {
        return (
            <BrowserRouter>
                <div>
                    <Link to="/">홈</Link>
                    <Link to="/photo">사진</Link>
                    <Link to="/rooms">방 소개</Link>
                    
                    <Route exact path="/" component={Home} />
                    <Route path="/photo" component={Photo} />
                    <Route path="/rooms" component={Rooms} />
                </div>
    
        );
    }
    
    ```
    - **BrowserRouter**
        - BrowserRouter 컴포넌트에서 현재 페이지의 상태값을 관리해준다.
    - **Link**
        - Link 컴포넌트를 사용하면 이 링크를 클릭했을 때 `to`에 해당하는 경로로 이동시켜준다
    - **Route**
        - Route 컴포넌트를 사용하면 `path` 값에 따라서 어떤 `Component`를 렌더링할지 결정을 해준다.
        - Route 컴포넌트로 렌더링을 하면 해당 컴포넌트의 속성 값으로 `match`라는 속성 값을 넣어준다. 이 `match` 안에는 `url`이라는 속성이 있는데, 이 속성 값이 의미하는 것은 Rooms 컴포넌트가 렌더링될 당시에 매치됐던 그 url의 일부를 의미한다.
            ```jsx
             ...
             export default function Rooms({ match }) {
                return (
                    <div>
                        ...
                        <Link to={`${match.url}/blueRoom`}>파란 방으로 이동하기</Link>
                        <Link to={`${match.url}/greenRoom`}>초록 방으로 이동하기</Link>
                        ...
                    </div>
                );
             }
            ```
