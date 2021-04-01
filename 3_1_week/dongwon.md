# 개인 프로젝트를 진행하면서 발생한 이슈를 정리하면서 작성했습니다.

## 🎯 Redux-Persist 사용하여 Store 유지하기

머무르고 있는 페이지에서, useSelector로 store 상태만 가져와 사용하여 개발하던 도중, 새로 고침하면 기존의 store가 초기화되는 문제를 겪었습니다.
해결 방법으로 LocalStorage와 SessionStorage의 storage를 redux에서 사용하게 해주는 Redux-Persist 라이브러리를 사용하여 해결했습니다.

> 특정 상태의 인식 오류, 중복 key값 오류 이슈가 발생하여 추가로 다시 작성 예정입니다.

### 1. 파일 구성

- Reducer를 관리하는 reducer/index.js
- store를 생성하는 reducer/store.js
- App.js 에서 store를 제공하기 위한 /index.js

### 2. 기본 사용

#### reducer/index.js

```javascript
import { combineReducers } from "redux";
import cart from "./cart";
import loading from "./loading";

// Redcer-Persist
import { persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage";

const persistConfig = {
  key: "root",
  storage: storage,
};

const rootReducer = combineReducers({
  data,
  cart,
  loading,
});

export function* rootSaga() {
  yield all([dataSaga()]);
}

export default persistReducer(persistConfig, rootReducer);
```

- **storage : 웹의 localStoage**
  - **session Storage에 저장하고 싶으면 import storageSession from 'redux-persist/lib/storage/session**
- persisConfig = {} : 새로운 persist 선언
  - key : reducer의 어느 지점에서부터 데이터를 저장할 것 인지,
  - storage : 웹의 localStorage
  - whitelist: ["cart"] -> cart만 저장
  - blacklist: ["cart"] -> cart만 제외
- persistReducer(persisConfig, reducer) : persisConfig가 추가된 reducer 반환

#### reducer/store.js

```javascript
import { createStore, applyMiddleware } from "redux";
import { createLogger } from "redux-logger";
import rootReducer, { rootSaga } from "./index";
import createSagaMiddleware from "redux-saga";
import { composeWithDevTools } from "redux-devtools-extension";

// Redux-Persist
import { persistStore } from "redux-persist";

const logger = createLogger();
const sagaMiddleware = createSagaMiddleware();

export const store = createStore(
  rootReducer,
  composeWithDevTools(applyMiddleware(sagaMiddleware))
);

sagaMiddleware.run(rootSaga);

export const persistor = persistStore(store);

export default { store, persistor };
```

- const store = createStore(rootReducer) : persistConfig가 추가된 rootReducer로 store를 생성
- persistStore : 새로 고침, 종료해도 지속될 store 생성

#### index.js

```javascript
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { BrowserRouter } from "react-router-dom";
import { Provider } from "react-redux";

// Redux-Persist
import { store, persistor } from "./reducer/store";
import { PersistGate } from "redux-persist/integration/react";

ReactDOM.render(
  <Provider store={store}>
    <PersistGate persistor={persistor}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </PersistGate>
  </Provider>,
  document.getElementById("root")
);
```

- PersistGage : react로 개발한다면 App 컴포는터를 PersistGate로 매핑.
  persist store가 redux에 저장될 때까지 앱 UI 렌더링이 지연됩니다.

### 3. 참고

[redux-persist npm](https://www.npmjs.com/package/redux-persist)

## 🎯 Redux Saga를 이용한 API 요청 상태 관리

Redux Saga를 이용해 비동기적으로 api를 호출할 때 요청에 대한 5가지 상태 ( 요청 시작, 로딩 중, 요청 성공, 요청 실패, 로딩 끝 ) 관리를 다루는 글입니다.

### 1. 파일 구성

- 네트워크 요청을 위한 /lib/api.js
- loading 상태를 관리할 reducers 파일 /reducers/loading.js
- reducer, saga를 작성할 reducers 파일 /reducers/data.js
- useSelector, useDispatch를 사용할 component

### 2. 네트워크 요청 함수 작성

```javascript
// lib/api.js
import axios from "axios";

export const getCategories = () =>
  export const getStores = () => axios.get('http://localhost:4000/stores');
```

json-server를 이용하여 만든 json 파일 [🔗 json-server 사용](https://github.com/dongwonnn/TIL/blob/main/21.01/01.27.md)

### 3. loading 관리 reducer 작성

```javascript
// 액션 타입 정의
const START_LOADING = "loading/START_LOADING";
const FINISH_LOADING = "loading/FINISH_LOADING";

// 액션 생성 함수
export const startLoading = (requestType) => ({
  type: START_LOADING,
  requestType,
});
export const finishLoding = (requestType) => ({
  type: FINISH_LOADING,
  requestType,
});

// 초기화
const initialStete = {};

// 리듀서 작성
function loading(state = initialStete, action) {
  switch (action.type) {
    case START_LOADING:
      return {
        ...state,
        // action.payload = requestType
        // 즉, requestTtype: true
        [action.payload]: true,
      };
    case FINISH_LOADING:
      return {
        ...state,
        [action.payload]: false,
      };
    default:
      return state;
  }
}

export default loading;
```

#### 코드 설명

- loading start / finish 두 가지 경우의 action 타입을 정의
- 액션 생성 함수의 인자로 requestType 받아온다.
  (requestType은 saga가 실행될 때 saga의 액션 타입이 들어가게 됨)
- loading reducer 작성.
  **action.payload**는 requestType을 의미.
  requestType이 data/GET_STORES 라면, **data/GET_STORES : true**

### 4. 기본적인 reducer, saga 작성

```javascript
// reducers/data.js
import * as api from "../lib/api";
import { call, put, takeLatest } from "redux-saga/effects";

// loading 객체 생성 함수 가져오기
import { finishLoding, startLoading } from "./loading";

// 액션 타입 정의
const GET_STORES = "data/GET_STORES";
const GET_STORES_SUCCESS = "data/GET_STORES_SUCCESS";
const GET_STORES_FAILURE = "data/GET_STORES_FAILURE";

// 액션 생성 함수
export const getStores = () => ({ type: GET_STORES });

// SAGA 작성
function* getStoresSaga() {
  yield put(startLoading(GET_STORES));
  try {
    const stores = yield call(api.getStores);

    yield put({
      type: GET_STORES_SUCCESS,
      payload: stores.data,
    });
  } catch (e) {
    yield put({
      type: GET_STORES_FAILURE,
      payload: e,
      error: true,
    });
  }
  yield put(finishLoding(GET_STORES));
}

// SAGA 통합
export function* dataSaga() {
  yield takeLatest(GET_STORES, getStoresSaga);
}

// 초기값 설정
const initialStete = {
  stores: null,
};

// 리듀서 작성
function data(state = initialStete, action) {
  switch (action.type) {
    case GET_STORES_SUCCESS:
      return {
        ...state,
        stores: action.payload,
      };
    case GET_STORES_FAILURE:
    default:
      return state;
  }
}

export default data;
```

#### 코드 설명

- **3가지 aciton 타입 정의.** 각각 요청, 요청 성공, 요청 실패(GET_STORES, GET_STORES_SUCCESS, GET_STORES_FAILURE)
- saga 작성. dispatch로 인해 요청 액션 getStores가 호출 되면서 saga 실행
- saga가 실행되면서 첫 번째 **startLoading(GET_STORES)를 통해 store에는 data/GET_STORES: true 인 상태. 즉, 로딩 중 상태.**
- call을 통해 네트워크 요청, data를 저장하고, **요청에 성공했다면 GET_STORES_SUCCESS action, 실패했다면 GET_STORES_FAILURE action 실행**

### 5. useSelector, useDispatch를 사용할 component

```javascript
// components/allStores.js
// 개인적으로 사이드 프로젝트에서 사용하는 컴포넌트 입니다.
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { getStores } from '../reducers/data';
const AllStores = () => {
  const stores = useSelector((state) => state.data.stores);
  const loadingStores = useSelector((state) => state.data.GET_STORES);

  const storesDispatch = useDispatch();
  useEffect(() => {
    storesDispatch(getStores());
  }, [getStores, storesDispatch]);

  return (
    <div className="allStores">
      {loadingStores && '로딩 중'}
      {!loadingStores &&
        ....
      ))}
    </div>
  );
};

export default React.memo(AllStores);

```

#### 코드 설명

- useSelector Hooks를 사용하여 store(리덕스)에 접근, stores와 loading 상태를 저장

  - state.data.GET_STORES는 loading reducer에서 정의한 data.**GET_STORES: true ( 로딩 중 ) 혹은 data.GET_STORES: flase ( 로딩 끝 ) 정보**

- useDispatch를 이용해 네트워크 요청 action getStores dispatch
- **&& 연산자를 loadingStores의 상태에 따라 하나의 상태만을 위한 렌더링**
