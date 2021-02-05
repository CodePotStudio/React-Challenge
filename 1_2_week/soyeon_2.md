> 이 글은 이재승님의 [실전 리액트 프로그래밍](https://www.inflearn.com/course/%EC%8B%A4%EC%A0%84-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/) 강좌를 듣고 공부하면서 필요한 부분을 정리한 글입니다.  
> [개인 블로그](https://jess2.xyz/react/react-tip-3/) 에도 같은 내용을 작성했습니다.


# 0. Index
1. [리액트 훅](#1-리액트-훅)
2. [훅 직접 만들기](#2-훅-직접-만들기)
3. [훅 사용 시 지켜야할 규칙](#3-훅-사용-시-지켜야할-규칙)

<br>

# 1. 리액트 훅
### 1-1. 리액트 훅(Hook)이란
- 컴포넌트에 기능을 추가할 때 사용하는 함수
- 예를 들면, 컴포넌트에 상태값을 추가하거나 자식 요소에 접근하는 등의 기능을 추가하고 싶을 때 훅을 사용할 수 있다.
- 리액트 16.8 (2019년 2월)에 새로 추가된 기능이다.
- 그전에는 컴포넌트에 기능을 추가하고 싶을 때 클래스형 컴포넌트를 사용했었다.
- 그런데 이제 훅이 나오면서 클래스형 컴포넌트는 더 이상 사용을 안해도 된다.
- 클래스형 컴포넌트보다 리액트 훅이 장점이 많고 리액트 팀에서도 훅에 집중을 하고 있다. 그래서 새로 리액트 프로그램을 작성한다면 리액트 훅으로 작성하는 것을 추천한다.
    - [Reference 영상](https://mk-v1.kakaocdn.net/dn/if-kakao/conf2019/conf_video_2019/1_104_01_m1.mp4)
    
<br>

### 1-2. useState : 상태값 관리 Hook
```js
import React, { useState, useEffect } from 'react';

export default function App() {
  const [count, setCount] = useState(0); 

  function onClick() {
    setCount(count + 1);
    setCount(count + 1);
  }

  console.log('render called');

  return (
    <div>
      <h2>{count}</h2>
      <button onClick={onClick}>증가</button>
    </div>
  );
}
```
- `useState`함수는 초기값을 넣어서 호출한다
- `useState`함수는 배열을 반환하는데 첫번째 아이템은 **상태값**, 두번째 아이템은 **상태값 변경 함수**가 반환된다.
- **상태값 변경 함수는 비동기 이면서 배치(batch)로 처리된다.**
- 리액트는 효율적으로 렌더링 하기 위해서 여러 개의 상태값 변경 요청을 배치로 처리한다.
- 따라서 `onClick` 함수가 호출되어도 `setCount` 는 두 번 작성하였지만 `count` 값은 `1`씩만 증가하고 `console.log('render called');` 로그는 한 번만 출력된다.
- 상태값 변경 요청을 왜 비동기 이면서 배치로 처리할까? 만약에 동기로 처리한다면 상태값 변경 함수가 호출될 때마다 화면을 다시 그리기 때문에 성능 이슈가 생길 수 있다.

#### 해결 방법 - 상태값 변경 함수에 함수를 입력한다.
```js
import React, { useState, useEffect } from 'react';

export default function App() {
    const [count, setCount] = useState(0);

    function onClick() {
        setCount(v => v + 1);
        setCount(v => v + 1);
    }

    console.log('render called');

    return (
        <div>
            <h2>{count}</h2>
            <button onClick={onClick}>증가</button>
        </div>
    );
}
```

- **함수로 입력하면 처리되기 직전의 상태값을 매개변수로 받기 때문에 원하는대로 동작한다.**
- 여기서 `onClick` 이벤트 핸들러는 리액트 내부에서 관리되는 리액트 요소에 입력이 되어 있기 때문에 배치로 처리가 된다.
- 리액트 내부에서 관리하지 않는 외부에서 호출을 하는 경우에는 배치로 동작하지 않는다. 즉 그러한 경우에는 상태값 변경 함수를 호출할 때마다 렌더링이 발생한다.

<br>

### 1-3. useEffect : 부수 효과 처리 Hook
> 부수 효과란 외부의 상태를 변경하는 것을 말한다.

- 서버 API호출, 이벤트 핸들러 등록/해제 등의 부수 효과를 처리할 때 사용한다.
- 컴포넌트 렌더링 중에 부수 효과를 발생 시키는 것은 프로그램의 복잡도를 크게 증가 시키고 유닛 테스트를 작성하기 힘들어지는 등 순수 함수가 가지는 여러 장점을 포기하는 것이다.
- useEffect Example

    ```js
    ...
    export default function App() {
        const [count, setCount] = useState(0);
  
        useEffect(() => {
            document.title = `업데이트 횟수: ${count}`;
        });
  
        return <button onClick={() => setCount(count + 1)}>increase</button>;
    }
    ```
    - useEffect 함수의 첫 번째 매개변수로 함수를 입력한다.
    - 이 함수는 컴포넌트가 렌더링된 후에 호출이 된다.
    - 렌더링 결과가 실제 돔에 반영되고나서 비동기로 호출이 되는 것이다.
    - 이 첫 번째 매개변수 함수를 **부수 효과 함수** 라고 부른다
    
<br>

### 1-4. useEffect 와 의존성 배열
- Example - 사용자 정보를 렌더링 해주는 컴포넌트

    ```js
    import React, { useState, useEffect } from 'react';

    export default function Profile({ userId }) {
      const [user, setUser] = useState(null);

      useEffect(() => {{
        getUserApi(userId).then(data => setUser(data));
      }, [userId]);

      return (
        <div>
        {!user && <p>사용자 정보를 가져오는 중...</p>}
        {user && (
          <>
            <p>{`name is ${user.name}`}</p>
            <p>{`age is ${user.age}`}</p>
          </>
        )}
        </div>
      );
    }

    const USER1 = { name: 'mike', age: 23 };
    const USER2 = { name: 'jane', age: 31 };

    function getUserApi(userId) {
      return new Promise(result => {
        setTimeout(() => {
          result(userId % 2 ? USER1 : USER2);
        }, 500);
      });
    }
    ```

    - 위의 코드는 `userId`를 속성값으로 받아서 `getUserApi`라는 api 함수를 호출해서 해당 유저의 정보를 가져온 다음에 `user` 상태값을 변경해주는 기능이다.
    - 여기서 API 호출을 시뮬레이션 하기 위해서 `userId`가 홀수, 짝수일 때일 때 각각 다른 값을 리턴해주도록 하는 함수를 작성함.
    - 부수효과 함수는 렌더링이 끝나고 호출이 된다. 그런데 만약 렌더링이 자주 발생한다면 그 때마다 API를 호출하는 것은 비효율적이다.
    - **이럴 때는 두 번째 매개변수에 배열을 입력할 수 있다. 이 배열은 의존성 배열이다. 이 배열의 값이 변경될 때만 부수효과 함수가 실행된다.**
    - 만약 두 번째 매개변수에 빈 배열을 입력하면 부수 효과 함수는 컴포넌트가 마운트된 이후에 한 번만 호출될 것이다.
    - 여기에서는 `userId`를 입력했기 때문에, `userId` 값이 변경될 때 부수효과 함수가 실행된다.
    
<br>

### 1-5. 의존성 배열에 어떤 값을 입력해야 할까
- 부수 효과 함수에서 사용한 변수를 잘 봐야 한다. 컴포넌트의 상태값, 속성값, 컴포넌트 내부에서 정의된 지역변수, 지역함수 같은 것들은 모두 의존성 배열에 작성을 해줘야 한다.
- `getUserApi`는 외부에 있는 함수이기 때문에 입력하지 않아도 된다.
- `userId`는 속성값이기 때문에 입력을 해줘야 한다.
- `setUser` 상태값 변경 함수는 조금 특별한데, 이 함수는 값이 변경되지 않는다는 것이 보장된다. 그래서 상태값 변경함수는 예외적으로 의존성 배열에 입력하지 않아도 된다.
- 의존성 배열은 꼭 필요한 경우에만 입력하는 게 좋다. 예를 들어 새로운 상태값을 추가한 다음에 부수 효과 함수에서 사용을 하면 매번 사용한 변수를 의존성 배열에 추가해야 하는 번거로움이 생긴다.
- 의존성 배열을 잘못 입력하면서 많은 버그가 발생하니 주의하자.

<br>

### 1-6. 부수 효과 함수가 반환하는 값

- Example code

    ```js
    ...
    export default function WidthPrinter() {
      const [width, setWidth] = useState(window.innerWidth);

      useEffect(() => {
        const onResize = () => setWidth(window.innerWidth);
        window.addEventListener('resize', onResize); // 이벤트 리스너 등록

        return () => {
          window.removeEventListener('resize', onResize); // 이벤트 리스너 해제
        };
      }, []);

      return <div>{`width is ${width}`}</div>;
    }
    ```

    - 부수 효과 함수에서 반환하는 함수는 다음 부수 효과 함수가 호출되기 직전에 호출된다. 또는 컴포넌트가 언마운트 되기 직전에 마지막으로 호출된다.
    - 의존성 배열로 빈 배열을 입력하면 컴포넌트가 생성될 때만 부수 효과 함수가 호출되고, 부수 효과 함수가 반환하는 함수는 컴포넌트가 사라지기 직전에 한 번만 실행된다.

<br>

# 2. 훅 직접 만들기
### 2-1. 커스텀 훅
- 리액트가 제공하는 훅을 이용해서 커스텀 훅을 만들 수 있다.
- 로직 재사용이 가장 큰 장점.
- 커스텀 훅 만들 때는 훅 이름은 `use`로 시작하는 것이 좋다.
- 훅은 일반적인 함수로 작성할 수 있다.
- 레고 블록처럼 기존 훅을 이용해서 새로운 훅을 만들 수 있다. 

<br>

### 2-2. 예시: useUser 훅 만들기
- `userId`를 입력하면 `user`객체를 가져올 수 있는 커스텀 훅
- `useUser`를 사용하는 쪽에서는 `userId`만 입력해주면 `user`객체가 나오니까 굉장히 편리하고 직관적이다.
- `userId`가 변경되면 훅 내부에서 자동으로 api를 호출해서 사용자 정보를 가져올거고, `useUser` 훅 내부 상태값이 변경되면 자동으로 이 컴포넌트도 같이 새로운 유저와 함께 렌더링이 될 것이다.
- `user` 데이터를 가져오는 것은 비동기이지만 마치 `userId`를 넣으면 `user`가 바로 나오는 것 처럼 (마치 동기 프로그래밍 방식 처럼) 간편하게 작성을 할 수가 있다.

<br>

### 2-3. 예시: useMounted 훅 만들기
- 보통 서버사이드 렌더링을 할 때 많이 쓰는 정보인데 마운트 됐는지 안됐는지에 대한 정보를 알기 위한 훅을 만들면 좋다.
- 상태값 변경 함수는 렌더링이 끝난 다음에 호출되는 것을 이용하는 개념이다.
- 단, 매번 호출될 필요는 없으니까 `useEffect`에 빈 배열을 넣어주어 초기에 한 번만 실행이 되도록 한다.
    
    ```js
    export default function useMounted() {
      const [mounted, setMounted] = useState(false);
      
      useEffect(() => {
        setMounted(true);
      }, []);
    
      return mounted;
    }
    ```

<br>

### 2-4. 기타 커스텀 훅 예시들
- 로그인된 사용자만 접근할 수 있는 페이지다 라고 할 때, 커스텀 훅을 만들어놓고 로그인된 사용자가 아닐 경우에는 얼럿을 띄워 준다던가 아니면 자동으로 로그인 페이지 같은데로 보내줄 수 있다. (`useBlockIfNotLogin()`)
- 어떤 페이지에서 사용자가 작성한 내용이 있을 때, 저장되지 않고 페이지를 벗어나려고할 때 확인 팝업 띄울 때도 커스텀 훅을 만들어서 관리하면 좋을듯. (`useBlockUnsavedChange(description)`)
- `useEffect`를 실행을 하는데 로그인 유저인 경우에만 실행을 하고 싶을 때 (사용법은 `useEffect`와 같은데 콜백을 호출하는 시점이 로그인 유저인 경우에만 호출을 해준다) (`useEffectIfLoginUser(callback, deps)`)
- 로컬 스토리지 사용 커스텀 훅 (`useLocalStorage(key, initalValue) ⇒ [value, setValue]`)

<br>

# 3. 훅 사용 시 지켜야할 규칙
> 아래의 두 가지 규칙을 지켜야 리액트가 각 훅의 상태를 제대로 기억할 수가 있다.

1. 하나의 컴포넌트에서 훅을 호출하는 순서는 항상 같아야 한다.
    - if문 안에서 훅을 사용하면 안된다. if문을 사용하게 되면 조건문에 따라 사용하는 훅이 달라질 수 있기 때문이다.
    - for문 안에서 훅을 사용하면 안된다. 반복문의 조건에 따라서 훅을 사용하는 횟수가 달라질 수 있기 때문이다.
    - 함수 안에서 훅을 사용하면 안된다. 함수가 항상 호출된다는 보장이 없기 때문이다.
2. 훅은 함수형 컴포넌트 또는 커스텀 훅 안에서만 호출되어야 한다.
    - 클래스형 컴포넌트의 메소드나 기타 일반 함수에서도 사용할 수 없다.
    - 훅은 함수형 컴포넌트를 위한 기능이기 때문이다.

