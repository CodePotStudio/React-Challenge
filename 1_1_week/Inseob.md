# Hooks API - useMemo

# 어째서 찾아보게 되었는가

> memoize 관련 처리가 제대로 되지 않아 개별 컴포넌트의 local state나 참조하고 있는 global state가 변경될 때마다 불필요한 렌더링이 발생하는 케이스가 있습니다.

이전에 N사 자회사의 과제 전형에 탈락하면서 받았던 피드백이 있었다. state 를 갱신할 때 굳이 할 필요 없는 부분까지도 불필요하게 렌더링을 하면서 memoization 처리가 제대로 되지 않았다는 이야기였는데, 찾아봤더니 `useMemo` 라는 hook 이 있었다. 

# 다시 정리하기: 메모이제이션이란

메모이제이션이란 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장하여 동일한 계산의 반복 수행을 제거하는 과정을 의미한다. 

피보나치 수열을 생각하면 된다. 재귀를 통해(Recursion) 피보나치를 구현하는 방법이 있으나, call stack 에 함수 그 자체를 집어넣기 때문에 연산해야 하는 횟수가 많아질수록 속도가 매우 느려진다. 

그래서 반복문과 배열 혹은 반복문과 변수를 통해 이전에 계산했던 값을 참조하는 간단한 수준의 메모이제이션으로 더욱 빠르게 계산할 수 있다는 방법을 제시해줬던 때 메모이제이션이라는 기법을 처음 알게 되었다.

# useMemo of React

[React 의 Hooks 문서 중 7번 Hook API 참고서에서는 `useMemo` 를 이렇게 설명한다.](https://ko.reactjs.org/docs/hooks-reference.html#usememo)

> “생성(create)” 함수와 그것의 의존성 값의 배열을 전달하세요. useMemo는 의존성이 변경되었을 때에만 메모이제이션된 값만 다시 계산 할 것입니다. 이 최적화는 모든 렌더링 시의 고비용 계산을 방지하게 해 줍니다.

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

간략하게 요약하자면, 위의 메모이제이션의 정의와 비슷하게 특정 함수와 그 함수가 의존하는 배열을 parameter 로 전달하면, dependency 가 변경되었을 때에 메모이제이션 된 값만 다시 계산을 하여 불필요한 연산을 줄인다고 한다. 

## example of useMemo - velpert

[velopert 님의 useMemo 에 대한 설명이 적힌 문서](https://react.vlpt.us/basic/17-useMemo.html)이다. 간단한 예제로 useMemo 가 일반적인 연산보다 어떻게 연산을 줄일 수 있는지를 설명해주는 글이다. 

### before use useMemo

```jsx
function countActiveUsers(users) {
	console.log('활성 사용자 수를 세는 중...');
	return users.filter(user => user.active).length;
};

//...

const count = countActiveUsers(users);
```

해당 로직을 통해, user 라는 object 중 acitve 가 true 인 경우를 filter method 로 걸러낸 뒤, .length 를 통해서 active user 가 얼마나 되는지를 구하는 과정이 있다. 

그러나 이 때 굳이 필요 없는 렌더링이 발생한다. 바로 input tag 의 값이 바뀔 때이다. 우리는 일반적으로 `useState` hook 을 통해 input value 를 관리하고, event object 에 `onChange` 를 건 뒤 그 안에 `setState` 함수를 넣어 state 를 변경한다. state 는 변경되면서 rendering 을 다시 수행하게 된다. 

물론 예시에 나온 것처럼 모든 로직들을 App component 에 다 넣진 않고 컴포넌트를 떼어내는 방법 또한 존재하겠지만, 이렇게 input tag 가 하나의 컴포넌트 안에 같이 존재하는 이상, 다시 렌더링 되는 건 당연한 일이다. 

### after use useMemo

```jsx
const count = useMemo(() => countActiveAllUsers(users), [users]);
```

이때 useMemo 를 통해 count 를 계산해 준다면, 위의 input tag 가 변경되는 과정 때문에 컴포넌트가 다시 렌더링이 되더라도 해당 값은 굳이 다시 연산하지 않는다. useMemo 의 두 번째 parameter 로 넣어준 array 안의 `users` 라는 배열이 바뀌지 않는 이상! 

이렇게 하게 되면, 기존에 input tag 에 입력을 할 때마다 다시 계산이 이뤄졌던 `count` 라는 변수를 구하기 위한 연산을 할 때, `users` 배열이 바뀌지 않는 이상 다시 연산하지 않고 마지막에 계산했던 값을 끌어와 사용함으로써 원하는 값을 구할 때 불필요한 연산을 줄일 수 있다.