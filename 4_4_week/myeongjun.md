# 4주차

### 상태관리와 Redux

- React로 웹 어플리케이션을 개발하다 보면 수많은 상태를 관리해야 됩니다.
- 여기서 상태란 컴포넌트 내의 mutable 데이터이고, 상태가 바뀔 경우 컴포넌트가 Rerendering됩니다.
- 예를 들어 상위 컴포넌트에서 5 Level정도 아래의 컴포넌트에 데이터를 전달해야 될 경우가 생길 수 있습니다.
- 이럴 경우 Props로 하위 컴포넌트, 하위 컴포넌트, ..., 하위컴포넌트로 전달 해야됩니다.
- 위와 같은 구조로 프로그램을 만든다면 Data flow가 어마어마하게 복잡해질 것이고, 어떤 오류가 발생했을 경우, 그 오류의 원인을 찾기 힘들어질 경우가 생길 수 있습니다.
- 이러한 문제점을 해결하기 위해 application 외부에서 상태를 관리하여 Data flow를 단순화 할 수 있습니다.
- React에서 사용할 수 있는 상태관리 중 대표적으로 Redux가 있습니다.
- Redux에서의 Data Flow
  - React 컴포넌트에서 변화가 일어남
  - 변화가 일어난 컴포넌트에서 Redux로 Dispatch
  - action생성함수에서 action을 객체형태로 만들어서 reducer로 반환
  - reducer는 변화를 일으키는 함수이며, 이전상태와 action을 받아와서 새로운 상태를 반환
  - 새로운 상태는 Store를 통하여 React에 새로운 상태를 subscribe

### Redux의 규칙

- 하나의 Application는 하나의 Store만 가지고 있습니다.
- read only이며 immutable해야 됩니다.(concat, filter, map, slice와 같이 불변성을 지키는 내장함수 사용)
- reducer는 순수함수여야 합니다.(이전 상태를 변경하면 안되고, 새로운 상태 객체를 만들어 반환합니다.)
- 동일한 parameter로 호출된 reducer는 항상 같은 결과를 반환해야 됩니다.

### Redux의 주요 키워드

1. Action

- 상태에 변화가 필요할 경우 action을 발생시킵니다.
- Action은 type이 필수적입니다.
- Action생성함수는 parameter를 받아와서, action객체를 만들어주는 함수입니다.

2. Reducer

- 변화를 일으키는 함수입니다.
- state와 action 2가지를 parameter로 받아옵니다.
- action은 type을 가지고 있고, 해당 action의 type에 따라 state를 업데이트 합니다.
- default는 기존 상태를 반환합니다.

3. Store

- 현재 state, reducer와 내장함수(dispatch, subscribe 등)가 포함되어 있습니다.
- subscribe를 호출할 떄 parameter로 특정 함수를 넣어주면 action이, dispatch될 때 마다 해당 함수를 실행하며, store가 업데이트 될때마다 함수를 실행할 수 있습니다.

### Redux 사용 예시

- 개인 프로젝트 진행히며, 상태관리 라이브러리로 Redux를 사용했는데, User정보, 화면Size를 Redux로 관리 하고 있습니다.
- User정보를 가져올 경우 Redux Thunk를 사용하여 비동기 처리를 하도록 했으며, 3가지 action이 있는데, 유저 프로필을 조회하는 loading상태, 유저 프로필을 조회 완료한 상태, 유저 프로필 조회 도중 오류가 난 상태 3가지 입니다.
- 화면 사이즈는 화면 크기 변형에 따라, 반응형 웹을 구성하기 위해 사용 중이고, 화면이 Resize되는 한가지 action만 가지고 있습니다.
- Redux를 작성할 때 다음과 같은 순서로 작성하였습니다.

  - action 정의

  ```tsx
  export const GET_USER_PROFILE = "GET_USER_PROFILE";
  export const GET_USER_PROFILE_SUCCESS = "GET_USER_PROFILE_SUCCESS";
  export const GET_USER_PROFILE_ERROR = "GET_USER_PROFILE_ERROR";
  export const getUserProfile = createAsyncAction(
    GET_USER_PROFILE,
    GET_USER_PROFILE_SUCCESS,
    GET_USER_PROFILE_ERROR
  )<undefined, UserProfile, AxiosError>();
  ```

  - action type 정의

  ```tsx
  export type UserAction = ActionType<typeof getUserProfile>;
  export type UserState = {
    userProfile: {
      loading: boolean;
      error: Error | null;
      data: UserProfile | null;
    };
  };
  ```

  - action 초기화

  ```tsx
  export const InitialUserProfile = {
    userProfile: {
      loading: false,
      error: null,
      data: null,
    },
  };
  ```

  - reducer함수 작성

  ```tsx
  export default function userReducer(
    state: UserState = InitialUserProfile,
    action: UserAction
  ) {
    switch (action.type) {
      case GET_USER_PROFILE:
        return {
          ...state,
          userProfile: {
            loading: true,
            error: null,
            data: null,
          },
        };
      case GET_USER_PROFILE_SUCCESS:
        return {
          ...state,
          userProfile: {
            loading: false,
            error: null,
            data: action.payload,
          },
        };
      case GET_USER_PROFILE_ERROR:
        return {
          ...state,
          userProfile: {
            loading: false,
            error: action.payload,
            data: null,
          },
        };
      default:
        return state;
    }
  }
  ```

  - redux thunk작성

  ```tsx
  export function getUserThunk() {
    return async (dispatch: Dispatch) => {
      const { request, success, failure } = getUserProfile;
      dispatch(request());
      try {
        const userProfile = await requestUserProfile();
        dispatch(success(userProfile));
      } catch (e) {
        dispatch(failure(e));
      }
    };
  }
  ```
