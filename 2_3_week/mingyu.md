---
title: React reconciliation & Fiber
author: mingyu.gu
---

# React reconciliation
Reconciliation은 React의 "비교(diffing)" 알고리즘입니다. 이 비교 알고리즘 덕분에 컴포넌트의 갱신이 예측 가능해지면서도 고성능 앱이라고 불러도 손색없을 만큼 충분히 빠른 앱을 만들 수 있습니다

###  React rendering logic
리액트 컴포넌트가 화면에 렌더링 되는 과정은 다음과 같습니다.
1. 리액트의 JSX가 `React.createElement`로 바벨에 의해 트랜스파일링됨
2. `React.createElement`함수 호출에 의해 리액트 엘리먼트 트리가 반환됨
3. React의 `reconciliation` 알고리즘에 의해 리액트 엘리먼트 트리를 재귀적으로 순회하면서 이전 트리와 현재 트리의 변경사항을 비교한다음 변경된 부분만 실제 DOM에 반영함 (Virtual-DOM)

## Overview
React를 사용하다 보면, "`render()` 함수는 React 엘리먼트 트리를 만드는 것이다." 라고 생각이 드는 순간이 있습니다. state나 props가 갱신되면 `render()`함수는 새로운 React 엘리먼트 트리를 반환하기 때문입니다. 이때 React는 방금 반환된 트리에 맞게 가장 효과적으로 UI를 갱신하는 방법을 알아야할 필요가 있습니다.

하나의 트리를 가지고 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘 문제를 풀기 위한 일반적인 해결책들은 n개의 엘리먼트가 있는 트리에 대해 O(n^3)의 복잡도를 가집니다.

React에 이 알고리즘을 적용한다면, 1000개의 엘리먼트를 그리기 위해 10억 번의 비교 연산을 수행해야합니다. 이를 대체하기 위해 React는 두 가지 가정을 기반하여 O(n) 복잡도의 휴리스틱 알고리즘을 구현했습니다.

1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.
2. 개발자가 `key` prop을 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.

실제로 거의 모든 사용 사례에서 이 가정들은 들어맞습니다.

## 비교 알고리즘 (Diffing Algorithm)
두 개의 트리를 비교할 때, React는 두 엘리먼트의 루트(root) 엘림먼트부터 비교합니다. 이후의 동작은 루트 엘리먼트 타입에 따라 달라집니다.

### 엘리먼트의 타입이 다른 경우
두 루트 엘리먼트의 타입이 다르면, React는 이전 트리를 버리고 완전히 새로운 트리를 구축합니다. `<a>` 에서 `<img>`로 바뀌는 등 트리 전체를 재구축하는 경우입니다.

트리를 버릴 때 이전 DOM 노드들은 모두 파괴됩니다. 컴포넌트 인스턴스는 `componentWillUnmount()`가 실행됩니다. 새로운 트리가 만들어 질 때, 새로운 DOM 노드들이 DOM에 삽입됩니다. 그에 따라 컴포넌트 인스턴스는 `componentDidWillMount()`가 실행됩니다. 이전 트리와 연관된 모든 state는 사라집니다.

루트 엘리먼트 아래의 모든 컴포넌트도 언마운트되고 그 state도 사라집니다. 예를 들어, 아래와 같은 비교가 일어나면,

```jsx
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```
이전 `Counter`는 사라지고, 새로 다시 마운트가 될 것입니다.

### DOM 엘리먼트의 타입이 같은 경우
같은 타입의 두 React DOM 엘리먼트를 비교할 때, React는 두 엘리먼트의 속성을 확인하여, 동일한 내역은 유지하고 변경된 속성들만 갱신합니다. 예를 들어,
```jsx
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```
이 두 엘리먼트를 비교하면, React는 현재 DOM 노드 상에 `className`만 수정합니다.

`style`이 갱신될 때, React는 또한 변경된 속성만을 갱신합니다. 예를 들어,
```jsx
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```
위 두 엘리먼트 사이에서 변경될 때, `fontWeight`는 수정하지 않고 `color`속성 만을 수정합니다.

DOM 노드의 처리가 끝나면, React는 이어서 해당 노드의 자식들을 재귀적으로 처리합니다.

## 자식에 대한 재귀적 처리
DOM 노드의 자식들을 재귀적으로 처리할 때, React는 기본적으로 동시에 두 리스트를 순회하고 차이점이 있으면 변경을 생성합니다.

예를 들어, 자식의 끝에 엘리먼트를 추가하면, 두 트리 사이의 변경은 잘 작동할 것입니다.

```jsx
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

React는 두 트리에서 `<li>first</li>`가 일치하는 것을 확인하고, `<li>second</li>`가 일치하는 것을 확인합니다. 그리고 마지막으로 `<li>third</li>`를 트리에 추가합니다.

하지만 위와 같이 단순하게 구현하면, 트리의 맨 앞에 엘리먼트를 추가하는 경우 성능이 좋지 않습니다. 예를 들어, 아래의 두 트리 변환은 형편없이 작동합니다.

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
React는 `<li>Duke</li>`와 `<li>Villanova</li>` 종속 트리를 그대로 유지하는 대신 모든 자식을 변경합니다. 이러한 비효율은 문제가 될 수 있습니다.

### Keys
이러한 문제를 해결하기 위해, React는 `key` 속성을 지원합니다. 자식들이 key를 가지고 있다면, React는 key를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인합니다. 예를 들어, 위 비효율적인 예시에 `key`를 추가하여 트리의 변환 작업이 효율적으로 수행되도록 수정할 수 있습니다.

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

이를 위해 데이터 구조에 ID라는 속성을 추가해주거나 데이터 일부에 해시를 적용해서 key를 생성할 수 있습니다. 해당 key는 오로지 형제 사이에서만 유일하면 되고, 전역에서 유일할 필요는 없습니다.

최후의 수단으로 배열의 인덱스를 key로 사용할 수 있습니다. 만약 항목들이 재배열되지 않는다면 이 방법도 잘 동작할 것이지만, 재배열되는 경우 비효율적으로 동작할 것입니다.

인덱스를 key로 사용 중 배열이 재배열되면 컴포넌트의 state와 관련된 문제가 발생할 수 있습니다. 컴포넌트 인스턴스는 key를 기반으로 갱신되고 재사용됩니다. 인덱스를 key로 사용하면, 항목의 순서가 바뀌었을 때 key 또한 바뀔 것입니다. 그 결과로, 컴포넌트의 state가 엉망이 되거나 의도하지 않은 방식으로 바뀔 수도 있습니다.

## Reference
- https://ko.reactjs.org/docs/reconciliation.html
- https://simsimjae.tistory.com/473?category=384814