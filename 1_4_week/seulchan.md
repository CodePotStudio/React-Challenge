# React Challenge 3주차

이번 기회에 써보거나 정리해보고 싶었던 개념.

다음 내용들은 챌린지가 진행되면서 추가/제거될 수 있음.

- next.js
  - [x] complete next.js tutorial
  - [x] read next.js documentation
  - [ ] next.js debugging
  - [ ] simple project with next.js
- react concept
  - [ ] About useCallback
  - [ ] lifecycle
- mobx (or redux)
- others
- [ ] socket.io (chat in react)
- [ ] css tools (styled component?)
- [x] infinite scroll & lazy loading

# Infinite scroll

프로젝트에서 무한 스크롤을 구현해야 할 일이 있어 구현하면서 관련된 내용을 정리해보았다.

무한스크롤은 기본적으로 컨텐츠의 끝부분을 만나게 되면, 다음 페이지를 불러와서 더하는 것.
lazy load와 흡사하다.

컨텐츠(브라우저)의 끝부분임을 감지하는 방법에는 크게 두가지가 있다.

- scroll event: 브라우저 높이를 사용하여 구현
- IntersectionObserver

전통적인 스크롤 감지 방식으로도 구현할 수 있지만, 이 경우 스크롤이 실행되는 순간마다 스크롤 이벤트가 발생되게 되므로 좋은 방법이 아닐거라고 판단하여 `IntersectionObserver`를 사용하여 구현하였다.

## Instersection Observer

이번에 처음 알게 된개념. mdn에서 설명하는 기본 개념은 다음과 같다.

> The Intersection Observer API provides a way to asynchronously observe changes in the intersection of a target element with an ancestor element or with a top-level document's viewport.

간단하게 정리하면 특정 DOM 엘리먼트가 viewport에 노출되었는지 여부를 비동기적으로 감시해주는 API이다.

Intersection observer를 생성하는 방법은 간단함. `IntersectionObserve`에 options과 callback 함수를 인자로 주어서 만들 수 있다.

문법

```
var observer = new IntersectionObserver(callback[, options]);
```

예제

```
let options = {
  threshold: 1.0
}

let callback = (entries, observer) => {
  entries.forEach(entry) => {
    if (entry.isIntersecting) {
      observer.unobserve(entry.target);
      console.log('seeing in screen');
    } else {
      console.log('out of screen');
    }
  }
}

let observer = new IntersectionObserver(callback, options);

observer.observe(
  document.getElementById('id')
)
```

[MDN web docs](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)에 컨셉, 사용법, 작동 방식이 자세하게 나와있지만 다 읽어보지는 않았다.

위 예시의 핵심적인 개념은 아래에 정리

- threshold: 정의된 퍼센테지만큼 노출/제외되면 entries 배열에 추가하여 callback 함수를 호출
  - root 등 다양한 옵션이 있음.
- callback, options을 인자로 받아 생성된 Intersection Observer (`observer`) 객체의 `observe` 메소드를 사용하여 특정 엘리멘트를 구독
  - options에 `root` 옵션으로 타겟 엘리먼트를 지정할수도 있음
- `entry.intersecting`으로 해당 entry의 노출 여부 확인
- `observer.unobserve` 메소드로 구독 해제 가능.

> 단점은 IE에서 지원하지 않는다는점. 어짜피 next.js를 사용하여 프로젝트를 구현하였기 때문에 IE는 고려하지 않음.

## IntersectionObserver with react

사실 IntersectionObserver의 사용법은 쉽게 숙지하였지만, 이를 react에서 적용하는데 꽤 애를 먹었다.

특히 헷갈렸던 부분은 callback 함수를 구현하는 부분과 state의 yaxis를 업데이트 하는 부분이었다.

아래는 구현된 예시. lazy loading으로 실제 컨텐츠를 추가하는 코드는 제거하였다.

```
import React, { useState, useRef, useCallback } from "react";

function IntersectionObserverExample(props) {
  const loadingRef = useRef(null);

  useEffect(() => {
    // fetch for the first time
    fetchMethod();
  }, [])
  
  const loadingCallback = useCallback((entries) => {
    const target = entries[0];
    // fetch only target is currently intersecting
    if (target.isIntersecting) {
      fetchMethod();
      console.log('changed');
    }
  }, [fetchMethod])

  useEffect(() => {
    props.fetchMethod();
    const observer = new IntersectionObserver(
      loadingCallback, //callback
      { threshold: 1 }
    );
    const target = loadingRef.current;
    observer.observe(target);
    // unobserve
    return () => observer.unobserve(loadingRef.current);
  }, [loadingRef]);
  
  return (
  <section>
    <section>
      // fetched data
    <section/>
      <section ref={loadingRef}>
        <section>Loading</section>
      </section>
  </section>
  )
}
```


그리고 IntersectionObserver로 observe 할 loading 영역은 `useRef` hook 사용.

loadRef가 변경될 때마다 IntersectionObserver 객체를 만들고, unobserve 시켜준다.

`loadingCallback` 함수는 그냥 일반 함수로 생성해도 되지만 함수를 재사용하기 위해 useCallback을 사용하였다. 마찬가지로 빈 리스트를 사용하여도 무방하지만 `fetchMethod`가 변경될 경우에는 해당 함수를 재생성하여야 하기 때문에 fetchMethod를 추가해주었다.


전체적인 흐름은 다음과 같다.

- 최초 렌더시 fetchMethod() 호출
- laadingRef가 변경되면서 IntersectionObserver 생성 및 등록
- 등록된 observer가 노출되었기 때문에 loadingCallback 함수 실행되고 fetch

> 처음 렌더 된 이후 loadRef를 viewport 밖으로 밀어내기 위해 fetchMethod()를 처음 호출하였는데, 처음 렌더 후 fetch되었을 때 loadingRef 영역이 viewport 밖으로 밀리지 않으면 이후 observer 생성이 안되는 증상이 있었기 때문.

## 정리

무한 스크롤을 구현해보면서 여러가지 새로운 개념을 배울 수 있었다.

- IntersectionObserver
- useCallback
- useRef

이후에 기회가 되면 useCallback과 useRef의 구현에 대해서도 정리해보면 좋을듯.
