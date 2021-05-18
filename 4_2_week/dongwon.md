## 페이지 이동 시 항상 맨 위로 스크롤 이동하기

페이지 이동 시 이전 페이지의 스크롤 값이 변하지 않아 다음 페이지에도 스크롤이 내려가있는 문제를 해결했습니다.

### 컴포넌트 준비

#### 다음 페이지 컴포넌트

```javascript
import React, { useEffect } from "react";
1️⃣import { useLocation } from "react-router";
import Details from "../components/Details";

const DetailPage = ({ match }) => {
  const storeId = match.params.storeId;

  const { pathname } = useLocation();

  2️⃣useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);

  return <Details storeId={storeId} />;
};

export default DetailPage;
```

1. `react-router`의 `useLocation hook`
   `useLocation hook`을 사용하면 `location` 객체에 접근할 수 있습니다.
2. `pathname`과 `window` 객체
   `location`의 `pathname`을 이용해 페이지 이동이 감지가 되면 `window`객체의 `scrollTo` 속성을 `(0,0)`으로 설정해 페이지를 맨 위로 이동시킵니다.

## 공통 컴포넌트 특정 페이지에서 제거하기

`header`나 `navbar`와 같이 모든 페이지에 있어야 하는 컴포넌트가 특정 페이지 (로그인, 회원가입)에는 보여지지 않아야 할 때도 있습니다.
`props`를 이용해 `isVisible`값과 같은 변수를 이용해 처리할 수 도 있지만, `react-rotuer`를 이용해서 처리해봤습니다.

### 컴포넌트 준비

#### react-router 설정 컴포넌트

저는 App.js에 router를 설정합니다. `Header`와 `Navbar`가 공통으로 필요한 컴포넌트 입니다.

```javascript
// app.js
import React, { useEffect } from 'react';
....

const App = () => {
 return (
   <div className="body">
     1️⃣<Switch>
       3️⃣<Route path="/login" component={LoginPage} />
       <Route path="/register" component={RegisterPage} />
       2️⃣<>
         <Header />
         <Navbar />
         <Route path="/" exact={true} component={MainPage} />
         <Route path="/search" component={SearchPage} />
         <Route path="/detail/:storeId" exact={true} component={DetailPage} />

         <Redirect from="*" to="/" />
       </>
     </Switch>
   </div>
 );
};

export default App;
```

1. `react-router1`의 `Switch`.
   `Switch`를 이용해 하나의 `Route` 혹은 `컴포넌트`만 선택할 수 있게 합니다.
2. 공통 컴포넌트(`Header`와 `Navbar`)와 필요한 페이지들을 `<>(Fragment)`를 이용해 하나로 묶었어, Switch로 인해 안에 있는 것들을 한 덩어리로 인식하게 했습니다.
3. 공통 컴포넌트가 필요없는 페이지들을 입니다. <> 바깥에 `Route`를 설정해 공통 컴포넌트의 영향을 받지 않게 구현했습니다.
