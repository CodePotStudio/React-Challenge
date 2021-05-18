## SWR 사용하기

로그인, 회원가입 처럼 한 페이지, 컴포넌트 안에서 네트워크 통신을 할 때, 비동기 통신이 하나밖에 없을 경우 굳이 Redux와 미들웨어들을 사용해 코드가 복잡해질 필요는 없습니다.
이 때 전역 상태 관리를 위해 ContextAPI와 같이 간단하게 사용할 수 있는 SWR을 사용해 전역 상태를 관리해봤습니다.

### 컴포넌트 준비

#### 기본적인 로그인 컴포넌트

```javascript
// login.tsx
import fetcher from '@utils/fetcher';
...

const LogIn = () => {
  1️⃣const { data, error, revalidate } = useSWR(
    "http://localhost:3095/api/users",
    2️⃣fetcher,
    {
      3️⃣dedupingInterval: 10000,
    }
  );

  const onSubmit = useCallback(
    (e) => {
      e.preventDefault();
      setLogInError(false);
      axios
        .post(
          "http://localhost:3095/api/users/login",
          { email, password },
          { withCredentials: true }
        )
        .then((response) => {
          console.log(response);
          4️⃣revalidate();
        })
        .catch((error) => {
          console.log(error.response);
          setLogInError(error.response?.data?.statusCode === 401);
        });
    },
    [email, password]
  );
};
```

1. SWR은 기본적으로 GET 요청을 통해 얻는 데이터를 저장합니다.
   `useSWR` 훅은 기본적으로 첫 번째 인자로 url, 두 번째 인자로 `fetcher` 함수를 통해 데이터를 가공하고 `return` 값을 요청 성공 시 `data`에, 에러 시 `error`에 저장합니다.
2. SWR는 기본적으로 일정 시간마다 자동으로 네트워크 통신을 합니다. 이를 통해 채팅방과 같은 네트워크 요청이 지속적으로 쌓이는 경우, 알아서 최신화를 시켜줍니다.
   하지만 너무 자주 호출하면 서버에 부담이 갈 수 있기 때문에, 자동으로 통신하는 것을 커스터마이징 할 수 있습니다.
   `revalidate`를 이용하면 `revalidate`함수가 호출 된 시점에 네트워크 요청을 할 수가 있습니다.
   지금 꼭 요청을 해야하는 시점, 로그인 정보를 가져와야하는 시점에 `revalidate`함수를 호출하면 바로 요청을 할 수 있습니다.
3. `useSWR`의 세 번째 인자로 일정 시간을 설정할 수 있는 `dedupingInterval` 속성을 이용해 설정할 수 있습니다.

#### fetcher 컴포넌트

```javascript
// fetcher.tsx
import axios from "axios";

const fetcher = (url: string) =>
  1️⃣axios
    .get(url, {
      withCredentials: true,
    })
    .then((res) => res.data);

export default fetcher;
```

1. axios를 이용해 GET 요청을 통해 `data`와 `error`를 리턴합니다.

## 코드 스플리팅 개념, 사용

코드 스플리팅은 하나으 큰 번들을 여러개의 작은 번들로 쪼개줍니다.
처음 리액트 앱을 실행했을 때 한꺼번에 많은 양을 로드해야 할 때, 코드 스플리팅을 이용하면 필요하지 않는 소스들은 로드 하지 않아 로딩 시간을 줄여주고 앱의 성능도 향상시킬 수 있습니다.
예를 들어 메인 페이지를 렌더링 해야하는데 굳이 로그인 페이지를 렌더링 할 필요가 없으므로 메인 페이지를 코드 스플리팅 할 수가 있습니다.

### Lodable 사용해서 스플리팅하기

Lodable 라이브러리를 이용해 쉽게 스플리팅 할 수 있습니다.
`npm i @loadable/component`
`npm i --save-dev @types/loadable__component` : TS 사용 시

#### App 컴포넌트

```javascript
import loadable from "@loadable/component";
import { Switch, Route, Redirect } from "react-router-dom";

1️⃣const Login = loadable(() => import("@pages/Login"));
const SignUp = loadable(() => import("@pages/SignUp"));

const App = () => {
  return (
    <Switch>
      <Redirect exact path="/" to="/login" />
      <Route path="/login" component={Login} />
      <Route path="/signup" component={SignUp} />
    </Switch>
  );
};

export default App;
```

1. 위에 설명한 것 처럼 페이지 단위로 코드 스플리팅할 수 있습니다. 따라서 보통 `Route`를 설정하는 `App` 컴포넌트에서 코드 스플리팅을 설정했습니다.
   `loadable` 라이브러리를 이용해 쉽게 설정할 수 있습니다.
   `const Login = loadable(() => import("@pages/Login"));`
