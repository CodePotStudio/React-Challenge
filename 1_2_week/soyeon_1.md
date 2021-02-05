> 이 글은 이재승님의 [실전 리액트 프로그래밍](https://www.inflearn.com/course/%EC%8B%A4%EC%A0%84-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/) 강좌를 듣고 공부하면서 필요한 부분을 정리한 글입니다.  
> [개인 블로그](https://jess2.xyz/react/react-tip-2/) 에도 같은 내용을 작성했습니다.

# 0. Index
1. [속성값과 상태값](#1-컴포넌트의-속성과-상태값)
2. [컴포넌트 함수의 반환값](#2-컴포넌트-함수의-반환값)
3. [리액트 요소와 가상돔](#3-리액트-요소와-가상돔)

<br>

# 1. 컴포넌트의 속성과 상태값
### 1-1. 속성값(Props)과 상태값(State)
리액트 컴포넌트에서는 UI 데이터를 속성값이나 상태값으로 관리를 해야 한다.

#### 상태값으로 관리되지 않은 코드 Example
```js
import React from 'react';

let color = 'red';

export default function App() {
  function onClick() {
    color = 'blue';
  }

  return (
    <button style={{ backgroundColor: color }} onClick={onClick}>
      좋아요
    </button>
  )
}
```
- 외부에 변수를 만들어놓고 그 변수를 수정할 수 있는 구조다.
- 하지만 클릭 이벤트가 발생해서 `onClick` 함수가 실행이 되어도 버튼 색이 파란색으로 변경되지 않는다.
- 이것은 `color` 변수값을 변경은 했지만 **리액트가 이 값이 변경됐다는 사실을 모르기 때문**이다.
- **리액트가 값의 변경 사실을 알려면 상태값으로 관리를 해야 한다.**

#### 상태값으로 관리된 코드 Example
```js
import React from 'react';

export default function App() {
  const [color, setColor] = useState('red');

  function onClick() {
    setColor('blue');
  }

  return (
    <button style={{ backgroundColor: color }} onClick={onClick}>
      좋아요
    </button>
  )
}
```
- `useState` 함수를 호출하면 컴포넌트에 상태값을 추가할 수 있다.
- `useState`의 매개변수인 `red`는 상태값의 초기값을 의미한다.
- `useState`는 배열을 반환한다. 배열의 첫 번째 아이템은 **상태값**이고 두 번째 아이템은 **상태값 변경 함수**이다.
- `const [color, setColor] = useState('red');` 여기서 배열 비구조화 문법이 사용되었다.
- 상태값 변경함수인 `setColor`를 호출해서 상태값인 `color`가 변경되면 **리액트는 자동으로 ui를 변경해준다.**
- 클릭을 하면 버튼 색이 파란색으로 변경된다.
    
        
#### 속성값으로 관리된 코드 Example
- 자식 컴포넌트
    ```js
    import React from 'react';

    export default function Title(props) {
      return <p>{props.title}</p>
    }

    // 위의 코드에서 객체의 비구조화 문법을 이용하면 아래와 같이 작성할 수 있다.
    // export default function Title({ title }) {
    // 	return <p>{title}</p>
    // }
    ```
    - `props` : 부모 컴포넌트로부터 전달 받은 속성값.
    - 객체의 비구조화 문법을 이용하면 사용할 때 `props.{속성명}` 을 입력하지 않고 바로 `{속성명}`으로 사용할 수 있기 때문에 좀 더 간편하게 작성할 수 있다.
    
- 부모 컴포넌트
    ```js
    import React, { useState } from 'react';
    import Title from './Title';

    export default function Counter() {
      const [count, setCount] = useState(0);
  
      function onClick() {
        setCount(count + 1);
      }
  
      return (
        <div>
          <Title title={`현재 카운트: ${count}`} />
          <button onClick={onClick}>증가</button>
        </div>
      );
    }
    ```

    - Counter 라는 컴포넌트에서 Title 컴포넌트를 자식 컴포넌트로 사용하고 있고 `title`이라는 속성값을 내려주고 있다.
    - 이 때 `count`라는 상태값을 기반으로 `title`값을 계산하고 있는데 `count` 값이 변경되면 Counter 컴포넌트는 다시 렌더링이 될거고 Title 컴포넌트도 다시 렌더링 된다. 이 때 새로 생성된 속성값을 받는다.
    - 부모 컴포넌트가 렌더링 될 때마다 자식 컴포넌트도 렌더링이 되는데, **이 때 자식의 속성값이 변경되지 않았을 때도 불필요하게 자식 컴포넌트가 렌더링 된다.**
        
<br>

### 1-2. React.memo

속성값이 변경될 때만 이 컴포넌트가 다시 렌더링 되게 하려면 `React.memo`를 사용할 수 있다.

```js
import React from 'react';

function Title({ title }) {
    return <p>{title}</p>
}

export default React.memo(Title);
```

<br>

### 1-3. 불변 변수로 관리되는 속성값과 상태값 
> 불변 변수란? 변수의 값을 바꿀 수 없는 것

#### 속성값
- 속성값은 불변 변수이기 때문에 값을 변경하려고 시도하면 에러가 발생한다.  
- 자식 컴포넌트에 전달되는 속성값은 상위 컴포넌트에서 관리하기 때문에 수정하지 못하도록 막혀있는 것이다.  

#### 상태값
- 상태값은 불변 변수가 아니지만 불변 변수로 관리하는 것이 좋다.
- 불변 변수로 관리하면 코드의 복잡도도 낮아지는 장점이 있다.
- 상태값을 불변 변수로 관리하지 않은 코드 Example

    ```js
    import React, { useState } from 'react';
    import Title from './Title';

    export default function Counter() {
      const [count, setCount] = useState({ value: 0 });
  
      function onClick() {
        count.value += 1;
        setCount(count);
      }
  
      return (
        <div>
          <Title title={`현재 카운트: ${count.value}`} />
          <button onClick={onClick}>증가</button>
        </div>
      );
    }
    ```

    - 버튼을 클릭해도 값이 증가되지 않는다.
    - 리액트는 상태값 변경 유무를 이전 값과의 단순 비교로 판단을 하는데 **count가 지금 객체고 그런데 여기서 객체의 참조값은 변하지 않았다.** 단순히 내부의 속성값만 변경된 상태다.
    - 그래서 **리액트 입장에서는 이 값이 변경되지 않았다고 판단한다. 그래서 setCount를 호출한 것이 무시된다.**
    - 따라서 상태값도 속성값과 마찬가지로 불변 변수로 관리하는 게 좋다.
    - 객체를 불변 변수로 관리하는 한 가지 방법은 전개 연산자를 이용하는 것이다.
    
- 상태값을 불변 변수로 관리한 코드 Example

    ```js
    import React, { useState } from 'react';
    import Title from './Title';

    export default function Counter() {
      const [count, setCount] = useState({ value: 0 });
  
      function onClick() {
        setCount({ ...count, value: count.value + 1 });
      }
 
      return (
        <div>
          <Title title={`현재 카운트: ${count.value}`} />
          <button onClick={onClick}>증가</button>
        </div>
      );
    }
    ```

    - 전개 연산자 문법에서 객체의 속성 값들을 풀어놓고 변경하고자 하는 값을 덮어 쓰는 방식이다.
    
<br>

# 2. 컴포넌트 함수의 반환값
컴포넌트에서 반환할 수 있는 값은 어떤 것들이 있는지 알아보자.

#### div 리액트 요소
```js
export default function App() {
    return <div>안녕하세요</div>;
}
```

#### 컴포넌트 
```js
export default function App() {
  return <Counter />;
}
```

#### 문자열 / 숫자 
```js
export default function App() {
  return '안녕';
}
```

#### 배열
```js
export default function App() {
  return [<p key={1}>world</p>, <p key={2}>hello</p>];
}
```
- **배열로 반환할 때는 리액트 요소가 항상 `key`를 가지고 있어야 한다.**
- `key`는 렌더링을 효율적으로 하기 위해서 필요하다. 리액트가 이 값을 이용해서 가상돔에서의 연산을 효율적으로 할 수가 있다.

#### Fragment
```js
export default function App() {
  return (
    <React.Fragment>
      <p>Hello</p>
      <p>World</p>
    </React.Fragment>
  );
}
```
- Fragment에서는 내부 요소의 순서가 일종의 `key` 역할을 하기 때문에 `key`를 입력하지 않아도 오류가 나지 않는다.
- **Fragment는 이렇게 여러 개의 요소를 반환할 때 유용하게 사용된다.**
- Fragment 기능은 비교적 최근에 추가된 기능인데, 이 기능이 없을 때는 `div` 태그로 감싸 줬다. 이 경우에는 원치 않는데 이렇게 `div`요소가 추가되는 단점이 있었다.
- 하지만 이렇게 Fragment를 사용하면 실제 dom에는 반영되지 않는다.
- **Fragment를 자주 쓰는 경우에는 아무것도 입력하지 않는 축약형으로 작성을 할 수가 있다. `<></>`**

    
#### null / boolean
```js
export default function App() {
  return (
    <div>
      {null}
      {false}
      {true}
    </div>
  );
}
```
- null 이나 boolean 값은 실제 화면에 출력되지 않는다.
- boolean 값은 아래와 같이 **조건부 렌더링**을 할 때 유용하게 사용된다.

```js
export default function App() {
  return (
    <div>
      {count.value > 0 && <Title title={`현재 카운트: ${count.value}`} />
    </div>
  );
}
```

#### Portal
- 리액트 Portal은 html에서 root 엘리먼트 말고 다른 멀리 떨어진 엘리먼트에 렌더링하고 싶을 때 사용할 수 있다.
- 예를 들어서 index.html이 아래와 같이 존재.

    ```html
    ...
    <body>
      <div id="root"></div>
      <div id="something"></div>
    </body>
    ...
    ```

- App.js

    ```js
    import React from 'react';
    import ReactDOM from 'react-dom';
    import Counter from './Counter';

    export default function App() {
      return (
        <>
          <p>안녕</p>
          <Counter />
          {ReactDOM.createPortal(
            <div>
              <p>hello</p>
              <p>world</p>
            </div>,
            document.getElementById('something'),
          )}
        </>
      );
    }
    ```

    - 리액트 Portal을 사용하기 위해서는 react-dom에 있는 함수를 사용해야 한다.
    - createPortal이라는 함수를 사용하고 두 번째 매개변수는 html에 있는 요소를 지정을 해준다. 그러면 렌더링이 `something` 요소 밑에 렌더링이 잘 된다.
    - **리액트 Portal은 보통 모달(Modal)을 위해서 많이 사용되기도 한다.**
    
<br>

# 3. 리액트 요소와 가상돔
- 리액트는 메모리에 가상돔을 올려놓고 이전과 이후의 가상돔을 비교하고 변경된 부분만 실제 돔에 반영한다.

### 3-1. element 구조
- example 1

    ```js
    const element = {
      <a key="key1" style={{ width: 100 }} href="https://google.com">
        Click here
      </a>
    }

    console.log(element);

    // log 출력 결과
    // {
    //   type: 'a',
    //   key: 'key1',
    //   ref: null,
    //   props: {
    //     href: 'https://google.com',
    //     style: {
    //       width: 100,
    //     },
    //     children: 'Click here',
    //   },
    //   ...
    // };
    ```

- example 2

    ```js
    function Title({ title, color }) {
      return <p style={{ color }}>{title}</p>;
    }

    const element = <Title title="hello" color="blue" />;
  
    console.log(element);

    // log 출력 결과
    // {
    //   type: Title,
    //   props: { title: 'hello', color: 'blue' },
    //   ...
    // }
    ```

    - 컴포넌트를 이용해서 리액트 요소를 만드는 경우에는 `type`을 보면 컴포넌트 함수가 입력이 되어 있다. 이 함수를 이용해서 리액트는 렌더링을 위한 충분한 정보를 얻을 수가 있다.
    - 리액트가 이 함수를 실행을 할텐데 그러면 `<p style={{ color }}>{title}</p>;` 이러한 값을 얻어 갈 수가 있다.
       
- 변경된 부분만 실제 돔에 반영 된다는 것을 한 번 확인해보자.
    - Example

        ```js
        import React, { useState, useEffect } from 'react';
        
        export default function App() {
          const [seconds, setSeconds] = useState(0);
        
          useEffect(() => {
            setTimeout(() => {
              setSeconds(v => v + 1);
            }, 1000);
          });
        
          return (
            <div>
              <h1>hello</h1>
              <p>지금까지 {seconds}초가 지났습니다</p>
            </div>
          );
        }
        ```
        
        - `seconds` 값이 1초마다 바뀌는데, `p` 태그 안의 `{seconds}` 부분만 변경된다.
        - 만약에 `div` 태그 `key`값에 `seconds`를 주게되면 `div` 돔 요소가 1초마다 삭제되고 다시 추가된다. 
        - 이렇게 `key`를 변경하면 리액트는 이것을 다른 요소라고 판단을 해서 이전 것을 삭제하고 새로 만들어서 붙인다.
        - 돔 요소 말고 컴포넌트의 `key`를 변경할 때는 어떻게 될까?
            - 컴포넌트의 `key`를 변경하게 되면 해당 컴포넌트는 삭제 되었다가 추가된다.
            - 이렇게 컴포넌트가 삭제되는 것을 Unmount라고 부르고 컴포넌트가 추가되는 것을 Mount라고 부른다.
            - 컴포넌트가 Mount될 때는 `useState`의 첫 번째 매개변수로 입력된 초기값이 상태값으로 할당이 된다. 즉, 초기화가 된다는 것이다. 따라서 1초에 한 번씩 `0`이 새롭게 할당된다.
            - 이렇게 `key`를 변경하면 컴포넌트는 Unmount와 Mount를 반복한다.
        
<br>

### 3-2. 리액트 요소 트리
- 리액트에서 데이터 변경에 의한 화면 업데이트는 랜더 단계와 커밋 단계를 거친다.
    - 랜더 단계 : 실제 돔에 반영할 변경사항을 '파악'하는 단계
    - 커밋 단계 : 파악된 변경 사항을 실제 돔에 '반영'하는 단계
    - 랜더 단계에서는 변경 사항을 파악하기 위해서 가상 돔을 이용한다. 가상돔은 리액트 요소로부터 만들어지고 리액트는 렌더링할 때마다 가상돔을 만들고 이전 가상돔과 비교를 한다.
    - 이는 실제 돔의 변경 사항을 최소화하기 위한 과정이다.

- 하나의 화면을 표현하기 위해서 여러 개의 리액트 요소가 트리 구조로 구성이 된다.
    ```html
    <div>
      <p>안녕하세요</p>
      <div>
        <p>이름: Jess2</p>
        <p>나이: 29</p>
      </div>
    </div>
    ```
    
    ```jsx
    {
      type: 'div',
      props: {
        children: [
          {
            type: 'p',
            props: {
              children: '안녕하세요',	
            },
          },
          {
            type: 'div',
            props: {
              children: [
                {
                  type: 'p',
                  props: {
                    children: '이름: Jess2',
                  },
                },
                {
                  type: 'p',
                  props: {
                    children: '나이: 29',
                  },
                },
              ],
            }
          },
        ],
      },
    }
    ```
    


