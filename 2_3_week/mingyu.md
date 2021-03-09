---
title: React reconciliation & fiber
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

# React Fiber

React Fiber는 React v16에 포함된 새로운 reconciliation algorithm 입니다.

React's core algorithm을 대부분 갈아엎은 2년 이상의 연구로 만들어진 알고리즘으로 React Fiber의 목표는 animation, layout, gestures와 같은 영역에 대한 rendering을 향상시키는 것 입니다. 

주요 특징은 **incremental rendering**으로 rendering work를 덩어리로 쪼갠 후 여러 프레임에서 적절히 수행시키는 것 입니다.

주요 포인트는 다음과 같습니다.
- UI에서 모든 업데이트를 즉시 적용할 필요가 없다. 실제로 그렇게 하면 frame drop과 사용자 경험이 저하될 수 있다.
- 업데이트마다 우선순위가 다르다. 애니메이션은 data update 보다 더 빨리 완료되어야한다.


## What is fiber?
UI를 다룰 때, 너무 많은 작업이 한 번에 실행될 때 문제가 생기기 마련입니다. 이럴 때 animation을 수행하게 되면 frame drop 과 사용자에게 툭툭 끊기게 보여지게 됩니다.

이를 해결하기 위해 작업들에 대해 우선순위를 매기고 `requestIdleCallback`은 유휴 기간 동안 낮은 우선순위 함수를 호출하도록 예약하고 `requestAnimationFrame`은 다음 animation에서 호출할 높은 우선 순위 함수를 예약하면 됩니다.
하지만 이러한 API를 사용하기 위해서는 **Incremental rendering**이 필요합니다. rendering 과정에는 단 하나의 call stack에만 사용하기에 스택이 비워질 때까지 다른 작업을 수행하지 않기 때문입니다.

UI rendering을 최적화하기 위해 call stack의 동작을 사용자 마음대로 정의할 수 있다면 좋지 않을까요? call stack을 마음대로 중단하고 stack frame을 수동으로 조작할 수 있다면 좋지 않을까요?
이것이 React Fiber의 목적입니다. Fiber는 React 컴포넌트에 특화된 stack의 재구현으로 **virtual stack frame**으로 생각하면 됩니다.

## Incremental rendering
- pause work and come back to it later
- assign priority to different types of work
- reuse previously completed work
- abort work if it's no longer needed

Fiber에서는 `requestIdleCallback`을 활용해서 동작중인 React 코드를 매번 부르고, 주어진 시간을 초과한다면 멈추고 더 중요한 일에 양보합니다. 더 중요한 일이 끝나면 다시 돌아와서 나머지 작업을 완료합니다.

어느 시점에서나 작업을 멈출 수 있어야 한다는 것은 task가 잘게 쪼개질 수 있어야 가능해 보입니다. 그리고 이러한 작업이 Incremental task 입니다.


# Phases

## Phase 1 (render / reconciliation)
interrupible

## Phase 2 (commit)
no interruptible

## Reference
- https://ko.reactjs.org/docs/reconciliation.html
- https://www.youtube.com/watch?v=ZCuYPiUIONs
- https://github.com/acdlite/react-fiber-architecture
