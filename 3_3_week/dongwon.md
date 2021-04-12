## JWT을 이용한 회원 인증, CORS 이슈 해결 ( React, axios )

JWT을 이용하여 로그인, 회원 인증과 백엔드 개발자님과 협업하는 과정에서 발생한 CORS 이슈 해결 과정을 정리했습니다.

### 1. 기본적인 로그인, 회원 인증 구현

- 서버에 email, password 전송, 맞다면 서버는 클라이언트에 jwt 토큰 전달
- 클라이언트는 토큰 저장 후, 서버에 전송, 유효성 체크

### 2. 로그인 api

기본적으로 Redux Saga를 이용하여 구현했습니다.

```javascript
// login api
// client : axios instance
export const login = ({ email, password }) =>
  client.post("/auth/login", {
    email,
    password,
  });

// saga
import * as authApi from '../lib/auth'

function* loginSaga(action) {
  yield put(startLoading(LOGIN));
  try {
    const { email, password } = action;
    1️⃣ const response = yield call(authApi.login, { email, password });
    2️⃣ const ACCESS_TOKEN = response.headers.authorization;

    localStorage.setItem("access_token", ACCESS_TOKEN);

    yield put({
      type: LOGIN_SUCCESS,
      3️⃣ payload: response.data,
    });
  } catch (e) {
    yield put({
      type: LOGIN_FAILURE,
      payload: e,
      error: true,
    });
  }
  yield put(finishLoding(LOGIN));
}

//reducer
...
    case LOGIN_SUCCESS:
      return {
        ...state,
        authError: null,
        auth: action.payload,
      };
    case LOGIN_FAILURE:
      return {
        ...state,
        authError: action.payload,
      };
...
```

1. input form 에서 입력받은 email, password를 yield call 을 이용해 api 호출
2. call을 통해 response가 도착하면 동작
3. 매번 api 호출 때 인증할 때 보낼 토큰 localStorage에 저장
4. 로그인 성공 시, auth state에 response body 저장

### 3. response headerds의 일부 데이터에 접근할 수 없을 때

- 로그인에 성공하면, 서버는 response headers에 토큰을 담아 전송합니다. 제 프로젝트는 백엔드 개발자님이 authorization을 통해 전달해 주고 있습니다.

```
// response headers
...
 authorization: eyJ0eXAiOi.....
 cache-control: no-cache,private
 connection: keep-alive
 content-type: application/json
 ...
```

- 하지만 로컬에서 실행했을 때, CORS 때문에 cache-control, content-type 의 정보만 response headers에 담겨 전송됩니다.

#### 3-1. proxy를 이용한 해결 방법

- 클라이언트에서 해결할 수 있는 방법으론, withCredentials 값을 true로 설정하면 response headers의 값에 모두 접근할 수 있습니다.

```javascript
axios.defaults.withCredentials = true;
// 이 프로젝트의 경우에는 client.defaults.withCredentials = true;
```

- 이 경우 CORS 이슈가 발생하는데, Proxy를 이용하여 간편하게 해결했습니다.

```javascript
// package.json
...
"proxy" : "[api 주소]"
...
```

#### 3-2. 서버에서 ACCESS-ORIGIN-EXPOSED-HEADERS 수정

- Proxy를 이용하면 local에서 테스트할 때는 가능하지만, 배포 단계로 넘어가면 작동하지 않습니다. 예를 들어, github pages로 배포를 하고 api 통신을 한다면, 서버 api 주소가 [name].github.io/login으로 설정이 되어있습니다.

- proxy를 사용한다면, headers의 내용이 모두 전송되지만, exposedHeaders를 설정한다면 보여주고 싶은 header만 전송이 가능합니다. proxy를 제거 후, 서버 쪽에서 headers에 노출하고 싶은 정보만 추가 설정.

```javascript
//
app.use(
  cors({
    exposedHeaders: ["Authorization"],
  })
);
```

### 4. 회원 인증

클라이언트에서 따로 저장한 토큰을 header에 설정, 서버에 전송 후 유효성 검사를 통해 인증 처리합니다.

```javascript
// api
export const check = () => client.get('/auth/user');

// saga
function* checkSaga() {
  yield put(startLoading(CHECK));
  try {
    const ACCESS_TOKEN = localStorage.getItem("access_token");
    1️⃣ client.defaults.headers.common["Authorization"] = `Bearer ${ACCESS_TOKEN}`;

    2️⃣ const response = yield call(authApi.check);

    yield put({
      type: CHECK_SUCCESS,
      payload: response.data,
    });
  } catch (e) {
    yield put({
      type: CHECK_FAILURE,
      payload: e,
      error: true,
    });
  }
  yield put(finishLoding(CHECK));
}

// redux
    case CHECK_SUCCESS:
      return {
        ...state,
        3️⃣ user: action.payload,
      };
    case CHECK_FAILURE:
      return {
        ...state,
        checkError: action.payload,
      };
```

1. header에 access_token 저장
2. 토큰 전송, 유효성 검사 후 response 반환
3. 인증됐다면 user state에 정보 저장

### 5. 브라우저 닫거나, 새로고침해도 로그인 상태 유지

- 로그인, 인증 성공 시 저장되는 user 정보를 localStorage에 저장

```javascript
useEffect(() => {
  if (user) {
    history.push("/");

    try {
      // user 정보 localStorage 저장
      localStorage.setItem("user", JSON.stringify(user));
    } catch (e) {
      console.log("localStorage is not working");
    }
  }
}, [history, user]);
```

- 브라우저를 새로 렌더링 했을 때, user정보가 있다면 다시 회원 인증.
  깜빡임 현상을 없애기 위해 index.js에 로직 구현

```javascript
function loadUser() {
  try {
    const user = localStorage.getItem("user");

    if (!user) return;

    dispatch(check());
  } catch (e) {
    console.log("localStorage is not working");
  }
}

loadUser();

ReactDOM.render(
  <Provider store={store}>
    <BrowserRouter basename="delivery-service">
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById("root")
);
```

### 추가

- 저와 백엔드 개발자님 둘 다 협업은 처음이라 일주일 동안 고생하며 CORS 이슈를 해결했지만 JWT 인증에 대해선 미숙한 것이 많았습니다.
  특히 서로의 작업환경에 대해서 잘 모르다 보니 의견 조율하는 데 어려움을 겪었고, 그러다 보니 제대로 구현하고 있는 게 맞는지 확신이 안 들었습니다.
- 프론트에서 JWT 처리 방식을 공부하다가 보안에 대해서 많이 알게 됐습니다. 특히 토큰 저장에 대해서 지금은 localStoage로 토큰을 저장했지만, 서버에서 api를 만들어보면서 다양한 방법을 통해 보안에 안전한 처리를 할 필요가 있는 것 같습니다.
