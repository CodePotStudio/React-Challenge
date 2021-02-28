A (Mostly) Complete Guide to React Rendering Behavior를 읽고 내용을 정리합니다. 1주차에 이어서 진행됩니다.

[https://blog.isquaredsoftware.com/2020/05/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior/](https://blog.isquaredsoftware.com/2020/05/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior/)

## Component Metadata and Fibers

`React`는 현재 컴포넌트 안에 포함된 모든 데이터들를 `fiber` 객체에 기록합니다.

`Fiber`가 저장하는 데이터들

- 어떤 컴포넌트를 그려야 하는지에 대한 정보
- 컴포넌트의 현재 props와 state
- 부모, 이웃, 자식 컴포넌트에 대한 정보
- rendering을 하는 과정에서 사용되는 메터 데이터들

`fiber` 객체는 실제 컴포넌트에 대한 `props`, `state`에 대한 값들을 가지고 있습니다. `React`는 `props`나 `state`를 컴포넌트에서 사용하려고 할 때, 해당 컴포넌트에 `fiber` 객체에 접근할 권한을 주는 방식으로 작동합니다.

`fiber`는 컴포넌트를 `rendering`하는데 있어서 중요한 구현체인 것 같은데, 지금은 크게 중요한 것 같지 않아 이 정도로 알고 지나가겠습니다.

## Component Types and Reconciliation

`React`에 똑같은 `DOM tree`에 있는 `HTML node`를 렌더링하려고 요청하면 React는 바닥에서 새로 만드는 것이 아니라 기존에 쓰던 것을 재활용하고, 적절하게 업데이트가 필요한 내용만 변경합니다. 즉, `React`가 컴포넌트의 인스턴스를 새로운 렌더링을 요청할 때까지 인스턴스를 유지하고 있다가 같은 요청이면 동일한 인스턴스를 반환해준다고 볼 수 있습니다.

`Class` 컴포넌트에서는 클래스의 인스턴스를 사용하면 되지만, `functional` 컴포넌트에서는 인스턴스라는 개념이 없습니다. 그러나 `functional` 컴포넌트에서는 <MyFunctionComponent />처럼 컴포넌트 호출 자체를 하나의 인스턴스의 개념이라고 볼 수 있으며, `functional` 컴포넌트에서는 새로운 렌더링을 요청할 때까지 위 컴포넌트를 유지할 것입니다.

그렇다면 React는 어떻게 결과물이 실제로 변경되었는지 확인할까요?

첫번째 방법으로는 `===` 를 활용하여 각 엘리먼트의 레퍼런스를 비교합니다. 만약 기존에 있던 컴포넌트와 새로운 컴포넌트의 인스턴스가 다르다면 `React`는 현재 존재하는 `DOM nodes`를 포함한 컴포넌트의 `tree`를 전부 부수고, 다시 밑바닥부터 생성합니다.

여기서 큰 시사점이 하나 있는데, 새로운 컴포넌트를 렌더링하는 과정에서 만들게 되면 무한 렌더링에 빠질 수 있습니다.

```jsx
function ParentComponent() {
	// This creates a new `ChildComponent` reference every time!
	function ChildComponent() {}

	return <ChildComponent />;
}
```

위와 같이 `ParentComponent` 가 렌더링 되는 과정에서 `ChildComponent` 컴포넌트를 만들면 렌더링되는 과정에서 `ChlidComponent`가 매번 새로 생기며 레퍼런스가 바뀌기 때문에 무한 렌더링에 빠질 수 있습니다.

```jsx
// This only creates one component type reference
function ChildComponent() {}

function ParentComponent() {
	return <ChildComponent />;
}
```

컴포넌트를 여러 개 구성할 때는 아래와 같이 함수를 구분해 주어야 합니다.

두번째 방법은 `key props`를 이용하는 방법입니다. `key`는 `pseudo props`로 실제 컴포넌트로 전달되지는 않습니다. `React`는 `key`를 동일 컴포넌트의 여러 인스턴스를 구분하는 용도로 사용합니다.

### key를 사용하는 원리

React는 기본적으로 동시에 두 리스트를 돌면서 차이점이 있으면 변경된 내용을 생성합니다. 예를 들어, 자식의 끝에 엘리먼트를 추가하면, 두 트리 사이의 변경은 잘 작동할 것입니다.

```jsx
// 1
<ul>
  <li>first</li>
  <li>second</li>
</ul>

// 2
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

`React`는 두 트리에서 `<li>first</li>`가 일치하는 것을 확인하고, `<li>second</li>`가 일치하는 것을 확인합니다. 그리고 마지막으로 `<li>third</li>`를 트리에 추가합니다.

하지만 위와 같이 단순하게 구현하면, 리스트의 맨 앞에 엘리먼트를 추가하는 경우 성능이 좋지 않습니다.

```jsx
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

이렇게되면 기존에 있던 `<ii>Duke</li>`, `<li>Villanova</li>` 가 있어야 할 자리에 `<ii>Duke</li>`, `<li>Villanova</li>` 가 없기 때문에 전체가 변경된 것임을 인지하고 전체를 다시 렌더링합니다.

### key를 사용하면 해결되는 문제점들

자식들이 `key`를 가지고 있다면, `React`는 `key`를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인합니다. 예를 들어, 위 비효율적인 예시에 `key`를 추가하여 트리의 변환 작업이 효율적으로 수행되도록 수정할 수 있습니다.

```jsx
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

이제 `React`는 '2014' `key`를 가진 엘리먼트가 새로 추가되었고, '2015'와 '2016' `key`를 가진 엘리먼트는 그저 이동만 하면 되는 것을 알 수 있습니다.

즉, 앞 뒤 혹은 중간에 추가되더라도 `key`가 변경되지 않았음을 확인하고 위치만 변경시켜 이전에 진행되었던 비효율적인 프로세스에서 효율적으로 렌더링을 할 수 있게 바꿔줄 수 있습니다.

### key를 사용할 때 주의해야 하는 점

**key는 형제들을 구분하는데에 사용되는 값이므로, 형제들 사이에서 유일한 값으로 사용하여야 합니다. 즉, 전역에서 유일한 필요는 없습니다.** 그렇다고 list의 `index`를 사용하게 되면 항목의 순서가 바뀔 때 `key`도 바뀌게 되므로 의도치 않은 방식으로 렌더링이 될 수 있으므로 주의해야 합니다.
