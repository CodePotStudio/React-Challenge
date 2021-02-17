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

의존성이 변경되었을 때에만 메모이제이션된 값을 다시 계산하게된다.

명쾌하게 알 수 있는 블로그글을 보았다.

https://leehwarang.github.io/2020/05/02/useMemo&useCallback.html

코드샌드박스 : https://codesandbox.io/s/upbeat-margulis-v8k51?file=/src/Info.js



## React.memo()

학습하면서 처음본 내용이 있는데, 바로 `React.memo` 이다.

리액트는 컴포넌트를 렌더링한 뒤, 이전 렌더된 결과와 비교하여 DOM 업데이트를 결정한다.

컴포넌트가 `React.momo()` 로 래핑되면, 리액트는 컴포넌트를 렌더링하고 결과를 메모이징한다.

그리고 다음 렌더링이 일어날때 컴포넌트가 넘겨받는 `props` 가 같다면, 메모이징된 컴포넌트를 재사용한다.

```javascript
export function Book({title, author}) {
  return (
  	<>
    	<h1>Book Title: {title}</h1>
			<div>Book author: {author}</div>
    </>
  )
}

export const MemoBook = React.memo(Book);
```

`MemoBook` 는 메모이징된 컴포넌트이다.

즉, `MemoBook` 의 렌더링 결과가 메모이징되어있는 것이다.

`props`로 내려받는 `title` 이나 `author` 가 변경되지 않는다면, 다음 렌더링이 발생할 때 기존 메모이징된 결과를 그대로 사용하게 된다.

```javascript
// 최초 렌더링.
// 리액트는 MemoBook 함수를 호출한다.
<MemoBook title="클린코드" author="로버트 마틴"/>

// 동일한 props로 렌더링할 때, 리액트는 MomoBook 함수를 호출하지 않는다. 
<MemoBook title="클린코드" author="로버트 마틴"/>

// props가 변경되면 리렌더링한다.
<MemoBook title="RxJS반응형프로그래밍" author="폴 대니얼스, 루이스 아텐시오"/>
```









## 레퍼런스

https://www.daleseo.com/react-hooks-use-callback/

https://ui.toast.com/weekly-pick/ko_20190731

https://leehwarang.github.io/2020/05/02/useMemo&useCallback.html