# React Hook

- 리액트 16.8에 추가



## useCallback

메모이제이션된 콜백을 반환한다.

```javascript
const memoCb = useCallback(() => {
  () => {
    doSomething(a, b);
  }
}, [a, b]);
```

첫번째 인자로 넘어온 함수를, 두번째 이자로 넘어오느 배열 내의 값이 변경될 때까지 저장 후, 재사용한다.

리액트 컴포넌트는 모두 함수이다.

컴포넌트 함수 안에 선언되어 있는 함수들은, 해당 컴포넌트가 렌더링될 때 마다 매번 새롭게 생성된다.

useCallback을 사용하여 해당 컴포넌트가 렌더링되더라도 그 함수가 의존하는 값들이 변하지 않는 한 기존 함수를 계속해서 반환하게 된다.



### Case1

```javascript
import React, { useState, useEffect } from "react";

const Member({userId}) => {
  const [user, setUser] = useState(null);
  
  const fetchUser = () => {
    fetch(`https://exmaple.com/users/${userId}`)
    	.then((res) => res.json())
    	.then(({user}) => user);
  }
  
  useEffect(() => {
    fetchUser().then(user => setUser(user));
  }, [fetchUser])
}
```

useEffect 함수는 의존성배열에 등록된 `fetchUser` 함수가 변경될 때에만 호출된다.

1. `fetchUser` 함수는 컴포넌트가 렌더링될 때 마다 생성된다.

2. 그러면 useEffect함수도 실행된다.

3. 그러면 `user` 상태값이 변경된다.

4. 그러면 또 다시 렌더링이 발생한다.

5. 그러면 또 다시 1번부터 반복된다....



이런 경우를 방지하기위해 `useCallback` 을 사용할 수 있다.

```javascript
import React, { useState, useEffect, useCallback } from "react";

const Member({userId}) => {
  const [user, setUser] = useState(null);
  
  const fetchUser = useCallback(() => {
    fetch(`https://exmaple.com/users/${userId}`)
    	.then((res) => res.json())
    	.then(({user}) => user);
  }, [userId])
  
  useEffect(() => {
    fetchUser().then(user => setUser(user));
  }, [fetchUser])
}
```

`fetchUser` 함수는 컴포넌트가 props로 내려받는 `userId` 가 변경되지 않는 한, 계속 재사용된다.
따라서 `useEffect()` 함수에서 끊임없이 호출되는 상황을 방지할 수 있다.



## useMemo

메모이제이션된 값을 반환한다.

사용법은 `useCallback` 과 동일하다.

```javascript
const memoValue = useMemo(() => expensiveFunc(a, b), [a, b]);
```

의존성이 변겨오디었을 때에만 메모이제이션된 값을 다시 계산하게된다.







