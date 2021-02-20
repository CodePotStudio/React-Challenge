A (Mostly) Complete Guide to React Rendering Behavior를 읽고 내용을 정리합니다.

[https://blog.isquaredsoftware.com/2020/05/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior/](https://blog.isquaredsoftware.com/2020/05/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior/)

## what is rendering?

React에서의 렌더링은 각 컴포넌트에 가장 최신의 props와 state를 포함해서 어떻게 화면에 그려야 할지 물어보는 과정입니다.

### rendering process

렌더링 프로세스가 진행되는 동안 `React`는 `root` 컴포넌트에서 자식 컴포넌트까지 돌면서 `rendering` 이 필요한 모든 컴포넌트를 찾고, 정보를 수집합니다. `rendering`할 컴포넌트의 정보들의 수집이 완료되면 `React`는 기존의 `DOM`과 `Virtual DOM`을 비교하여 실제 DOM에 적용할 변경사항들을 수집합니다. 이처럼 `DOM`을 비교하고, 계산하는 프로세스를 `React`에서 "`reconciliation`"이라고 부릅니다. 이 과정이 지나면 `React`는 변경사항을 실제 DOM에 순차적으로 하나씩 적용합니다.

### render와 commit phase

React 팀에서는 이를 두 단계로 나누어 표현합니다.

- render phase: 컴포넌트의 렌더링과 변경사항 비교하는 과정
- commit phase: 변경 사항을 실제 dom에 적용하는 과정

commit phase가 지나면 컴포넌트와 관련된 모든 ref를 업데이트 합니다. 그리고 나서 `componentDidMount`, `componentDidUpdate` 와 같은 class life cycle 함수를 실행하고, `useLayoutEffect` hooks를 실행합니다. 그리고 나서 React가 설정한 짧은 timeout이 지난 뒤 모든 `useEffect` hooks를 실행합니다.

이 파트에서의 핵심은 **렌더링은 실제로 DOM을 업데이트하는 과정을 이야기 하는 것이 아니며, 컴포넌트에 눈에 보이는 변화가 없더라도 컴포넌트는 렌더링이 될 수 있다는 점**입니다.

## How Does React Handle Renders?

### Queuing Renders

최초 렌더링이 끝나고 나서, re-rendering 대기열에 넣는 방법은 여러 가지가 있습니다.

- Class components:
  - `this.setState()`
  - `this.forceUpdate()`
- Function components:
  - `useState` 의 setter 함수를 호출하는 법
  - `useReducer` 의 dispatch를 호출하는 법
- 기타:
  - `ReactDOM.render(<App>)` 를 강제 호출하는 법

### 일반적인 render 작동 방식

React는 **기본적으로 부모가 컴포넌트가 렌더링이 되면 부모 컴포넌트 아래에 포함된 모든 자식 컴포넌트를 돌면서 렌더링**을 합니다.

예를 들어, `A > B > C > D` 순의 컴포넌트 구조가 하나 있다고 가정하겠습니다. 그리고 유저가 B 컴포넌트에 있는 counter를 업데이트하는 버튼을 클릭합니다.

- B 컴포넌트에 있는  `setState()` 을 호출하면 B가 re-render 대기열에 들어 갑니다.
- React starts the render pass from the top of the tree
- React가 컴포넌트 최상단부터 렌더링을 하기 시작합니다.
- React가 A 컴포넌트는 업데이트가 필요 없는 것을 판단하고, 다음으로 넘어 갑니다.
- React가 B 컴포넌트는 업데이트가 필요 한 것을 확인하고, rendering을 합니다.
- C는 업데이트가 필요하지 않지만, 부모 컴포넌트인 B가 렌더링 되었기 때문에 C 컴포넌트가 렌더링 됩니다.
- D도 마찬가지로 업데이트가 필요하지 않지만, 부모 컴포넌트 C가 렌더링 되었기 때문에 D 컴포넌트도 렌더링 됩니다.

즉 요약하면,

**컴포넌트를 렌더링 하는 것은 기본적으로 안에 포함된 모든 컴포넌트를 렌더링 시키는 것입니다.**

정상적인 렌더링에서 React는 `prop`가 변경에 따라 렌더링 여부를 확인하지 않습니다. 단지 부모가 re-rendering되면 무조건적으로 자식을 렌더링 할 뿐입니다.

그래서 만약 `<App>` 컴포넌트에서 `setState()` 함수를 호출한다면 `App` 내에 있는 모든 컴포넌트를 re-rendering 하게 됩니다. 이렇게 됨으로써 모든 컴포넌트가 실제 DOM과 변경사항이 있는지를 확인하게 되고, state를 업데이트 할 때마다 자원이 소모되게 됩니다.

영어로 된 문서를 정리하다보니, 생각보다 시간이 오래 걸리네요 ㅠㅠ

매주 시간을 꾸준히 내어 마저 정리해보도록 하겠습니다.
