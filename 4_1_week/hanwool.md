# state 불변성을 유지하면서 CRUD하기

React에서는 렌더링에서 side effect를 최소화하기 위해 state를 불변한 상태로 관리하여야 합니다. React가 state의 불변성에 대해 궁금하시다면 [링크](https://ljs0705.medium.com/react-state%EA%B0%80-%EB%B6%88%EB%B3%80%EC%9D%B4%EC%96%B4%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-ec2bf09c1021)를 참조해 주세요. 그렇기 때문에 상태에 변화를 줄 때 새로운 객체 만들어 우리가 원하는 변화를 추가하곤 합니다. 오늘은 React에서 효과적으로 상태를 CRUD(create, read, update, delete)하는 방법을 알아보도록 하겠습니다.

## 예시 데이터

아래와 같이 앞으로 사용할 `users` 리스트를 하나 만들어 줍니다. `users`는 `user` 객체의 모음이며, 아래와 같이 `user` 객체는 `id`, `name`, `userName`으로 구성되어 있습니다.

```jsx
const users = [
	{ id: 1, name: "김한울", userName: "toycrane" },
	{ id: 2, name: "홍길동", userName: "kill" },
	{ id: 3, name: "브로디", userName: "brody" },
	{ id: 4, name: "캐리", userName: "carry" },
];
```

## users에 새로운 user 추가하기 (Create)

```jsx
const users = [
	{ id: 1, name: "김한울", userName: "toycrane" },
	{ id: 2, name: "홍길동", userName: "kill" },
	{ id: 3, name: "브로디", userName: "brody" },
	{ id: 4, name: "캐리", userName: "carry" },
];

const newUser = { id: 5, name: "사울", userName: "saul" };

// 잘못된 예시
// 기존의 users 객체에 newUser를 추가하여 불변성이 깨진다)
// newUsers에는 기존의 users의 객체 주소가 담기게 된다.
const newUsers = users.push(newUser);

// 정상적인 예시
// ... 비구조할당으로 새로운 list를 만들고, 해당 리스트 맨 뒤에 newUser를 추가하였다.
// newUsers에는 새롭게 생성된 리스트의 주소가 담기게 된다.
const newUsers = [...users, newUser];
```

## users에서 원하는 user만 골라내기 (Read, Delete)

`users` 객체에서 `id`가 1인 `user` 객체만 골라내어 새로운 리스트에 담는 방법을 알아 보도록 하겠습니다.

```jsx
const users = [
	{ id: 1, name: "김한울", userName: "toycrane" },
	{ id: 2, name: "홍길동", userName: "kill" },
	{ id: 3, name: "브로디", userName: "brody" },
	{ id: 4, name: "캐리", userName: "carry" },
];

// 정상적인 예시
const filteredUsers = users.filter((user) => user.id === 1);
console.log(filteredUsers);
// [{id: 1, name: "김한울", userName: "toycrane"}]
```

`filter` 함수는 ES6부터 도입된 함수로, **매개변수로 입력한 함수의 조건이 참이 되는 값들을 모아 새로운 배열을 생성**해주는 함수입니다. 그러한 이유로 기존의 `users`의 영향을 미치지 않고, 불변성을 유지하며 새로운 `filteredUsers` 리스트 객체를 생성할 수 있습니다.

filter 함수에 대해서 자세히 알아보기 ([링크](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter))

## users에서 특정 user의 데이터 변경하기 (Update)

이번에는 `users` 객체에서 `id`가 1인 `user` 객체만 골라내어 name을 토이로 바꾸어보도록 하겠습니다.

```jsx
const users = [
	{ id: 1, name: "김한울", userName: "toycrane" },
	{ id: 2, name: "홍길동", userName: "kill" },
	{ id: 3, name: "브로디", userName: "brody" },
	{ id: 4, name: "캐리", userName: "carry" },
];

// 정상적인 예시
updatedUsers = users.map((user) =>
	user.id === 1 ? { ...user, name: "토이" } : user
);
console.log(updatedUsers);
// [{id: 1, name: "토이", userName: "toycrane"},
// {id: 2, name: "홍길동", userName: "kill"},
// {id: 3, name: "브로디", userName: "brody"},
// {id: 4, name: "캐리", userName: "carry"}]
```

map 함수는 filter와 마찬가지로 ES6부터 도입된 함수로 **매개 변수로 입력된 함수의 결과들을 모아서 하나의 리스트를 새로 생성해 주는 함수**입니다.

즉, `users` 객체를 순회하면서 각 `user`의 `id`가 1번인지 체크하고 맞다면 이름을 **토이**로 변경한 새로운 `user`객체를, 다르다면 기존의 `user` 객체를 return합니다. 이를 통해 `users`의 불변성을 유지하면서 새로운 `updatedUsers` 객체를 만들 수 있게 됩니다.

이로써 간단하게 state를 변경할 수 있는 방법을 알아 보았습니다. 객체의 깊이가 깊어지면 위와 같은 방법으로 새로운 객체를 만드는 방법에는 한계가 있습니다. 그럴 때는 immutable과 같은 라이브러리를 사용해야만 합니다. ([https://immutable-js.github.io/immutable-js/](https://immutable-js.github.io/immutable-js/))

![./hanwool_assets/Untitled.png](state%20%E1%84%87%E1%85%AE%E1%86%AF%E1%84%87%E1%85%A7%E1%86%AB%E1%84%89%E1%85%A5%E1%86%BC%E1%84%8B%E1%85%B3%E1%86%AF%20%E1%84%8B%E1%85%B2%E1%84%8C%E1%85%B5%E1%84%92%E1%85%A1%E1%84%86%E1%85%A7%E1%86%AB%E1%84%89%E1%85%A5%20CRUD%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%20e573ecffba13471fab5535b09293c93f/Untitled.png)

이 블로그 글은 Code Pot, 리액트 챌린지의 과제로 작성되었습니다.

Code Pot, 리액트 챌린지가 궁금하다면? ⇒ [링크](https://www.notion.so/Code-Pot-React-2-a299e9ab5e4c4b97ae59028a90db9720)
