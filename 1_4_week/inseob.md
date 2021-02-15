# Redux 에 대해서

---

⚠️  이 글은 [velopert 님이 작성한 '리덕스, 어떻게 해야 잘 쓸까?' 라는 글을 보고 제게 맞게 정리한 글입니다.](https://ridicorp.com/story/how-to-use-redux-in-ridi/?utm_source=facebook&utm_medium=page_webfrontend&utm_campaign=how-to-use-redux-in-ridi&fbclid=IwAR2yqfgVi0AYyxIMdsCyMyPm4JpQD_5mapsNKN3ewgtaMoHTLBnT0DOKe8g)

# TL;DR

1. Redux 가 정말 필요한지 고민해보고, 꼭 필요하지 않고 용례가 맞아떨어진다면 `useContext` 를 사용하라
2. 만약 Redux 를 사용하게 된다면, `Redux Toolkit` 을 강력 추천한다
3. `TypeScript` 는 JS 생태계에선 더 이상 거스를 수 없는 대세
4. 다양한 상태 관리 라이브러리들, 그리고 facebook 의 `Recoil`

---

# 1. 용례가 맞는 경우엔 useContext 를 사용하라

## 선요약

- 컴포넌트(와 그 컴포넌트의 하위 컴포넌트들도 포함한)가 "어차피" 다시 렌더링이 되는 상황에서 global state 를 참조해야 한다면 useContext 를 사용해도 무방하다.
- 그러나 그런 상황이 아니라면 Redux 를 사용하는 게 더 적절하다.

## 비슷하지만 다른 둘

`useContext` (편의를 위해서 hook 기준으로 칭하겠습니다)와 `Redux` 의 작동 양상은 어찌 보면 비슷하다. 그렇기에 useContext 가 Redux 의 일부를 대체할 수도 있는 것 또한 사실이다. 그러나 이는 용례를 명확히 구분해야 할 필요가 있다.

## 차이가 있다면

- useContext 는 context 로 걸어둔 상태에서 내가 원하는 global state 가 아닌 다른 요소들이 변경된다고 하여도 component 의 re-rendering 이 발생한다.
- 반면 Redux 는 의존하는 global state 의 특정 값이 변경될 때에만 component 를 re-render 시킨다

그렇기 떄문에 정말 단순하고 성능을 신경쓰지 않아도 되는 경우 global state 를 만들어 주기 위해서 useContext 를 써도 무방하다. 그러나, component 가 커지고 또 복잡해 지는 경우엔 useContext 를 걷어내야 한다. 물론 관심사 분리를 잘 해주어 Context 단위로 끊어주는 것도 좋지만, 이 경우에는 component 의 재사용성이 떨어질 수 밖에 없다. 차라리 그럴 바에는 아무리 BoilerPlate Code 가 많다고 한들 Redux 를 쓰는 게 성능 면에서나 투입하는 시간 면에서나 더욱 이득이다.  

# 2. Redux 를 사용한다면 Redux Toolkit 을 사용하라

Redux 의 기능이 매력적이긴 하지만, Boiler Plate Code 가 너무 길어져서 꺼려하는 사람들도 분명히 있을 것이다. 그래서 2020 년도에 Redux 개발팀에서 [Redux Toolkit 을 개발](https://redux-toolkit.js.org/)했다. 특히 요즘 대부분 라이브러리/프레임워크를 소개하는 공식문서에 나와있는 Concept 들 중에서 가장 첫 번째로 Simple 이라는 키워드를 제시했고 그 밑에 설명으로 "Includes utilities to simplify common use cases like store setup, creating reducers, immutable update logic, and more" 라는 이야기를 하는 걸 보면, 또 주변 프론트엔드 개발을 하는 사람들에게도 종종 이야기가 들려오는 걸 보면 이것도 `TypeScript` 처럼 피할 수 없는 JS 생태계의 또 다른 거대한 흐름이 아닌가 생각이 들기도 한다. 게다가 Redux 를 개발한 개발진들이 내놓은 물건이기도 하니깐.

# 3. TypeScript 는 더 이상 거스를 수 없는 대세

사실 나부터도 JS를 쓰라고 하면 JS를 쓰지만, 사실상 TS를 통해 거의 대부분의 작업을 하는 상황이다. Type Check, Dto, interface 등을 사용할 수 있기 때문에 기존에 많은 사람들이 고민하고 내놓았던 더 나은 코드를 만들 수 있다는 게 강점이라고 해야할까. 

# 4. 다양한 상태 관리 라이브러리들, 그리고 facebook 의 `Recoil`

페이스북도 보다 못했는지(?) 본인들이 React 에 딱 맞는 상태관리 라이브러리 `Recoil` 을 내놓았다. 많은 사람들이 주목을 하고 있고, SSR 쪽에서 약간 미비한 상황만을 제외하면 사실상 Recoil 도 대세가 될 거 같은 느낌이 든다. 

---

JS 의 프론트엔드 쪽에만 국한한 게 아니라, 내가 지금 사용하고 있는 방법보다 더 나은 대안이 있다면 현실적인 이유로 사용하지 못 하더라도 꾸준히 공부하고 생태계의 흐름을 꾸준히 지켜봐야 한다는 걸 느낀 글이었다. 나도 이런 개발자가 되고싶다는 생각도 했고.