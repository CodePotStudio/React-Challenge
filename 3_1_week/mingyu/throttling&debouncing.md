---
title: Throttling & Debouncing
author: mingyu.gu
date: 2021.03.28
---

# Throttling & Debouncing
Throttle과 Debounce는 자주 사용되는 이벤트나 함수의 실행 빈도를 줄여 성능상에 이점을 가져오기 위해 사용되는 기법입니다.

## Throttling
Throttle은 일정 주기에 여러번 발생하는 이벤트를 한 번만 실행시키는 기법입니다.

## Debouncing
Debounce는 여러번 발생하는 이벤트에서 가장 마지막 이벤트만 실행시키는 기법입니다.

## Throttle 과 Debounce의 차이점
Debounce는 입력이 끝날때까지 무한적으로 기다리지만 Throttle은 입력이 시작되면 일정 주기로 실행됩니다.

## 구현
**입력에 따른 자동완성 코드로 예시를 들어보겠습니다.**
### Sample Code
만약 다음과 같은 코드에서 `안녕하세요` 라고 입력했을 때 api는 총 12번 호출되게 됩니다.
```js
const handleKeyup = (e) => {
  const value = e.target.value;
  fetch(`https://api.com/search?value=${value}`)
    .then((res) => {
      return res.json();
    })
    .then((res) => console.log(res));
}

document.getElementById("input").addEventListener('keyup', handleKeyup)
```
### Throttle
키가 입력되었을 때마다 api가 호출되는것이 아닌 0.5s 마다 api가 호출됩니다.
```js
let throttle = null;

function getAutoComplete(value) {
  fetch(`https://api.com/search?value=${value}`)
    .then((res) => {
      return res.json();
    })
    .then((res) => console.log(res));
}

const handleKeyup = (e) => {
  const value = e.target.value;
  if (!throttle) {
    throttle = setTimeout(() => {
      throttle = null;
      getAutoComplete(value);
    }, 500);
  }
}

document.getElementById("input").addEventListener('keyup', handleKeyup)
```

### Debounce
키가 입력되는 이벤트가 끝나고 0.2s 동안 이벤트가 발생하지 않다면 api가 호출됩니다.
```js
let debounce = null;

function getAutoComplete(value) {
  fetch(`https://api.com/search?value=${value}`)
    .then((res) => {
      return res.json();
    })
    .then((res) => console.log(res));
}

const handleKeyup = (e) => {
  const value = e.target.value;
  if (debounce) {
    clearTimeout(debounce);
  }

  debounce = setTimeout(() => {
      getAutoComplete(value);
    }, 200);
}

document.getElementById("input").addEventListener('keyup', handleKeyup)
```
## 마무리
Throttling 과 Debouncing 은 성능을 높힐 수 있는 정말 유용한 기법입니다. 예제에서는 autocomplete를 예시로 들었지만 지도 api등에서 사용할 때 매우 높은 성능 향상을 경험할 수 있을 것이라 생각됩니다.
underscore `_.debounce` 와 `_.throttle` 을 사용하면 쉽게 사용할 수도 있지만 직접 구현해보면서 어떻게 동작하는지 확인해볼 수 있었습니다.
