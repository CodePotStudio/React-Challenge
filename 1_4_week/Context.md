# Context API
- 단순 Global State의 사용을 위해서는 Context API만으로도 충분히 구현할 수 있다.
- Redux와의 비교대상은 아님(상태 관리 자체는 useState, useReducer가 하는 것이기 때문)
- 필요한 부분에 대해서만 Re-render를 하는 성능 최적화가 이루어지지 않았다.`(의존하지 않는 값이 바뀌어도 Re-render 발생)`
- 이에 따라 `관심사의 분리가 굉장히 중요`
- 다뤄야할 state가 많은 경우 `constate` library 고려
```js
import React, { createContext, useState, useContext } from "react";

const UserContext = createContext(null);

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  return (
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  );
}

function useUser() {
  return useContext(UserContext);
}

function UserInfo() { //Conext에 담긴 사용자 정보를 보여준다.
  const { user } = useUser();
  if (!user) return <div>사용자 정보가 없습니다.</div>;
  return <div>{user.username}</div>;
}

function Authenticate() { //사용자 상태 업데이트 버튼을 보여준다.
//클릭시 UserInfo가 리렌더링되면서, Authenticate도 함께 리렌더링 됨.
  const { setUser } = useUser();
  const onClick = () => {
    setUser({ username: "user_name" });
  };
  return <button onClick={onClick}>사용자 인증</button>;
}

export default function App() {
  return (
    <UserProvider>
      <UserInfo />
      <Authenticate />
    </UserProvider>
  );
}
```

```js
import React, { createContext, useState, useContext } from "react";

const UserContext = createContext(null);
const UserUpdateContext = createContext(null); //UserUpdateContext를 따로 분리

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  return (
    <UserContext.Provider value={user}>
      <UserUpdateContext.Provider value={setUser}>
        {children}
      </UserUpdateContext.Provider>
    </UserContext.Provider>
  );
}

function useUser() {
  return useContext(UserContext);
}

function useUserUpdate() {
  return useContext(UserUpdateContext);
}

function UserInfo() {
  const user = useUser();
  if (!user) return <div>사용자 정보가 없습니다.</div>;
  return <div>{user.username}</div>;
}

function Authenticate() {
  const setUser = useUserUpdate();
  const onClick = () => {
    setUser({ username: "velopert" });
  };
  return <button onClick={onClick}>사용자 인증</button>;
}

export default function App() {
  return (
    <UserProvider>
      <UserInfo />
      <Authenticate />
    </UserProvider>
  );
}

```