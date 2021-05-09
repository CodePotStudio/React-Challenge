# 이번주 한 일
## 컴포넌트 분리
- `React`는 대문자만 `JSX`로 인식 -> 반드시 파일 이름은 대분자로 작성
- `Button` 폴더 안에 있는 `index.js`를 가지고 올 때, `import Button from './Button'`만 써도 된다. ES6에서는 폴더에 `index.js` 파일이 있으면 지정하지 않아도 암묵적으로 가져온다고 한다.

## Props
- `props`는 `property`의 줄인말로 컴포넌트에 넘겨줄 속성을 의미한다.
- 컴포넌트에 속성으로 넘겨준 값들은 모두 `props`에 보관된다. 아래 코드에서 `onClick`을 넘겨준 것처럼 함수도 전달할 수 있다.
    ```
    // app.js
    ...
    import Card from "./Card";

    return (
        <Card
            title="리엑트 챌린지"
            subtitle="4주동안 진행하는 리엑트 코드팟 챌린지"
            onClick={() => open()} />
    )
    ...
    ```
    ```
    // components/Card/index.jsx
    import React from 'react';

    const Card = (props) => {
        return (
            <div>
                <div> {props.title} </div>
                <div> {props.subtitle} </div>
            </div>
        )
    }
    ```

## props.children
- 아래 코드를 실행하면 `버튼`은 나오지 않고, `Hello`만 출력된다.
    ```
    const Box = () => <div> Hello </div>

    const App = () => {
        return (
            <Box>
                <button>버튼</button>
            </Box>
        )
    }
    ```
- `Box` 내부에 있는 컴포넌트까지 렌더링하고 싶을 때 사용하는 것이 `props.children`이다.
    ```
    const Box = (props) => <div> Hello {props.children} </div>

    const App = () => {
        return (
            <Box>
                <button>버튼</button>
            </Box>
        )
    }
    ```

## React Router
- `React`에서는 라우팅을 따로 제공하지 않아서 관련 라이브러리를 설치해서 사용해야 한다. 그중 가장 많이 사용하는 라이브러리는 `React Router`이다.
    ```
    npm install react-router-dom
    ```
- Router 컴포넌트에는 여러 종류가 있는데 이번에 공부한 것은 `BrowserRouter`이다. 추후 다른 것들도 공부할 생각이다.

## Route 컴포넌트
- Router 컴포넌트는 정해진 URL로 접근했을 때, 약속된 컴포넌트를 렌더링하는 기능을 제공한다.
- 사용 방법은 두가지이다.
    ```
    // 첫번째 방법
    <Route path="url 경로" component={렌더링할 컴포넌트} />

    // 두번째 방법
    <Route path="url 경로">
        <렌더링할 컴포넌트 />
    </Route>
    ```
- 실제 사용 방법은 아래 코드와 같다. 마지막 `Route`에 `exact`를 넣어준 이유는 다른 `path`에도 `/`가 포함되어 있기 때문에 Home 컴포넌트로 렌더링된다. 이 문제를 해결하기 위해 `/` 일 경우에만 `Home` 컴포넌트를 렌더링하도록 추가한 것이다.
    ```
    import React from "react";
    import { BrowserRouter as Router, Route } from "react-router-dom";

    // Home, MyPage, About 컴포넌트 생성
    const Home = () => <div>Home</div>;
    const MyPage = () => <div>MyPage</div>;
    const About = () => <div>About</div>;

    // 각 URL로 보여줄 컴포넌트 매칭
    const App = () => {
        return (
            <Router>
                <Route path="/mypage" component={MyPage} />
                <Route path="/about" component={About} />
                <Route path="/" exact component={Home} />
            </Router>
        );
    };

    export default App;
    ```

## Link 컴포넌트
- Link 컴포넌트는 HTML에서 a태그와 비슷한 기능이다.
- 둘의 차이점은 불러오는 방식이다. `a 태그`는 페이지 전부를 새로 불러온다. 반면에 `Link 태그`는 브라우저의 URL만 바꾸고 필요한 컴포넌트만 업데이트한다.
- 작성은 아래와 같이 하면 된다.
    ```
    <Link to="이동할 URL">링크 이름</Link>
    ```
- 실제로 적용은 아래와 같이 한다.
    ```
    import React from "react";
    import { BrowserRouter as Router, Route, Link } from "react-router-dom";

    // Home, MyPage, About 컴포넌트 생성
    const Home = () => <div>Home</div>;
    const MyPage = () => <div>MyPage</div>;
    const About = () => <div>About</div>;

    // 각 URL로 보여줄 컴포넌트 매칭
    const App = () => {
        return (
            <Router>
                <ul>
                    <li>
                        <Link to="/">Home</Link>
                    </li>
                    <li>
                        <Link to="/mypage">MyPage</Link>
                    </li>
                    <li>
                        <Link to="/about">About</Link>
                    </li>
                </ul>
                <Route path="/mypage" component={MyPage} />
                <Route path="/about" component={About} />
                <Route path="/" exact component={Home} />
            </Router>
        );
    };

    export default App;
    ```

## React 이미지
- React에서 이미지 태그를 사용할 때 HTML에서 사용하듯 사용하면 안된다. 아래 코드와 같이 사용해야 한다.
    ```
    import cover from "이미지 경로/cover.jpg";

    <img src={cover} />;
    ```

<br/>
<br/>

# 한주 후기
하나의 서비스를 개발하며 전체적으로 React에 대해 파악했다. 미묘한 의문점이 몇 가지 들었지만 정확하게 React에 대해 알지 못하니깐 그 미묘함이 뭔지 모르겠어서 질문이나 검색을 못하는 것 같다. 그래서 다음주부터는 React를 자세하게 공부하기 위해 `velopert`님의 '리엑트를 다루는 기술' 책을 읽으며 알아가야겠다.


더불어 2주동안 리엑트 공부를 하면서 라이브러리를 잘 활용해야 될 것 같다는 것을 느꼈다. 물론 `Angular`나 `Vue`도 마찬가지겠지만 말이다... 

<br/>
<br/>

# 다음주 할 일
1. https://react.codepot.kr/ 에서 추가로 공유해주신 자료 읽기
2. '리엑트를 다루는 기술' 책을 읽으며, 리엑트 더 깊게 공부하기