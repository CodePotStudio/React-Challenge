# jest
- javascript testing library
- babel, ts, node, react, angular, vue 등에서 사용할 수 있다.(JQuery는..?)
## 특징
- Zero Config : 쉬운 설정으로 사용가능하게 한다.
- Snapshots : 큰 Object를 쉽게 테스트할 수 있는 snapshot test (테스트와 함께 실행되거나 inline에 embedded)
- Isolated : performance 극대화를 위해 병렬적으로 Test가 실행된다.
- Great API : it -> expect까지 한 곳에 전체 toolkit을 담을 수 있다. 

## Snapshot Test
- 예기치 않게 UI가 변경되지 않게 확인하는 유용한 Tool
- 보통 UI Component를 render 및 snapshot하고 Test와 함께 저장된 Snapshot file과 비교한다.
- 두 snapshot이 `match하지 않을 경우 fail`
- React에서 Graphical UI를 렌더링하는 대신, Test Renderer를 통해 React Tree에 대한 sirializable 값을 빠르게 생성할 수 있다.
### 예시
```js
import React from 'react';
import renderer from 'react-test-renderer';
import Link from '../Link.react';

it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="http://www.facebook.com">Facebook</Link>)
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```
- 위 테스트가 처음 실행될 때, Jest는 Snapshot file을 만든다.(이 파일은 commit 및 review되어야 함 -> commit할 때 넣으라는 말)
- Snapshot file을 만들 때 Pretty Format이 사용되어 readable한 코드가 된다.(잘 모르겠음..)
- 후속 테스트를 실행할 때, 기존 snapshot과 이번 output을 비교, match되면 pass(이 부분이 잘 이해가 안됨! 한 describe에서 후속 it인지, 아니면 첫 실행 이후 두 번째 실행시에 snapshot을 비교한다는 것인지) ->후자인거 같습니다

```js
// snapshot file
exports[`renders correctly 1`] = `
<a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}
>
  Facebook
</a>
`;
```
### 의도적인 변화에 의한 Snapshot Test Failure
- 아래와 같은 변화가 있을 경우
```js
it('renders correctly', () => {
  const tree = renderer // instagram으로 링크 바꿈
    .create(<Link page="http://www.instagram.com">Instagram</Link>)
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```
![](images/failedSnapshotTest.png)
- 명백히 바뀌어야 하는게 당연하지만, Fail 날 것임
```bash
# 아래 명령어로 해결
jest --updateSnapshot
#또는
jest -u
```

### Interactive Snapshot Mode
- Watch Mode에서 Fail된 Snapshot이 update될 수 있다.
- 아는 내용이라 스킵! (https://jestjs.io/docs/en/snapshot-testing#interactive-snapshot-mode)

### Inline Snapshot
- 스냅샷 값들이 source code에 자동으로 기록되는 것 외에는 일반 snapshot과 똑같이 동작함
- 아래와 같이 test file에 snapshot이 기록됨(좋은 건지는 잘 모르겠음, vim 개발을 하면 좋을 수도..)
```js
it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="https://prettier.io">Prettier</Link>)
    .toJSON();
  expect(tree).toMatchInlineSnapshot(`
<a
  className="normal"
  href="https://prettier.io"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}
>
  Prettier
</a>
`);
});
```
### Property Matchers
- 대부분의 경우 snapshot에 field가 생성된다.
- 이런 Object를 snapshot하기 위해서 테스트할 때마다 실패할 것임(값이 Dynamic하기 때문에)
```js
it('will fail every time', () => {
  const user = {
    createdAt: new Date(),
    id: Math.floor(Math.random() * 20),
    name: 'LeBron James',
  };

  expect(user).toMatchSnapshot();
});

// Snapshot
exports[`will fail every time 1`] = `
Object {
  "createdAt": 2018-05-19T23:36:09.816Z,
  "id": 3,
  "name": "LeBron James",
}
`;
```
- 위와 같은 경우를 위해 비대칭 matcher 제공
- 이 matcher는 snapshot이 쓰이거나 테스트되기전에 체크되며, value 대신 type으로 저장됨
```js
it('will check the matchers and pass', () => {
  const user = {
    createdAt: new Date(),
    id: Math.floor(Math.random() * 20),
    name: 'LeBron James',
  };

  expect(user).toMatchSnapshot({
    createdAt: expect.any(Date), //any
    id: expect.any(Number), //any
  });
});

// Snapshot
exports[`will check the matchers and pass 1`] = `
Object {
  "createdAt": Any<Date>,
  "id": Any<Number>,
  "name": "LeBron James",
}
`;
```
### Best Practice
1. 코드처럼 사용하기
    - commit 및 review되어야 한다.
1. Test는 deterministic해야 한다.
    - 한 Component에 대해 여러 번 Test를 실행해도 같은 결과를 내야 한다.
    - platform specific하거나 non-deterministic data를 포함하면 안된다.
    - ex) Clock 같은 것을 만들 때는 Mock으로 생성하자
1. 서술적인 snapshot명
    - 가장 좋은 이름은 snapshot content를 설명하는 것
    - 리뷰어가 리뷰하기 더 쉬워진다.
    - <Component/> should render as null 등

## Async
- API Call 등
```js
// User Data를 가져와서 Name을 Return하는 function
import request from './request';

export function getUserName(userID) {
  return request('/users/' + userID).then(user => user.name);
}
// request.js
const http = require('http');

export default function request(url) {
  return new Promise(resolve => { //Promise가 뭘까
    http.get({path: url}, response => { //HTTP Request Example
      let data = '';
      response.on('data', _data => (data += _data));
      response.on('end', () => resolve(data));
    });
  });
}
```
- 실제로 API 통신을 할 것이 아니기 때문에 Mocking이 필요하다.
```js
// __mocks__/request.js
//request.js의 Mock Manual을 만들기 위해 __mock__폴더에 생성(Case Sensitive Must LOWER)
const users = {
  4: {name: 'Mark'},
  5: {name: 'Paul'},
};

export default function request(url) {
  return new Promise((resolve, reject) => {
    const userID = parseInt(url.substr('/users/'.length), 10);
    process.nextTick(() =>
      users[userID]
        ? resolve(users[userID])
        : reject({
            error: 'User with ' + userID + ' not found.',
          }),
    );
  });
}

// __tests__/user-test.js
jest.mock('../request');

import * as user from '../user';
it('works with promises', () => {
  expect.assertions(1);
  return user.getUserName(4).then(data => expect(data).toEqual('Mark'));
});
```
- jest.mock("../request") 콜을 통해 manual mock을 만든다.
- it의 return value는 resolve될 Promise이어야 한다.

### .resolves
- resolve를 사용하면 조금 덜 장황하고, 다른 matcher와 함께 promise를 만족할 수 있다.
- promise가 reject되면 assertion은 fail
```js
it('works with resolves', () => {
  expect.assertions(1);
  return expect(user.getUserName(5)).resolves.toEqual('Paul');
});
```
### async / await
- @babel/preset-env 필요
```js
// async/await can be used.
it('works with async/await', async () => {
  expect.assertions(1);
  const data = await user.getUserName(4);
  expect(data).toEqual('Mark');
});

// async/await can also be used with `.resolves`.
it('works with async/await and resolves', async () => {
  expect.assertions(1);
  await expect(user.getUserName(5)).resolves.toEqual('Paul');
});
```

### Error handling
- catch로 error도 handling할 수 있다.
```js
// Testing for async errors using Promise.catch.
it('tests error with promises', () => {
  expect.assertions(1);
  return user.getUserName(2).catch(e =>
    expect(e).toEqual({
      error: 'User with 2 not found.',
    }),
  );
});

// Or using async/await.
it('tests error with async/await', async () => {
  expect.assertions(1);
  try {
    await user.getUserName(1);
  } catch (e) {
    expect(e).toEqual({
      error: 'User with 1 not found.',
    });
  }
});
```

### rejects
- rejects는 resolves처럼 동작한다.
- promise가 만족하면, test는 fail.
```js
// Testing for async errors using `.rejects`.
it('tests error with rejects', () => {
  expect.assertions(1); //필수는 아니지만, 특정 개수의 assertion이 호출되는 것을 확인하기 위해 추천됨
  return expect(user.getUserName(3)).rejects.toEqual({
    error: 'User with 3 not found.',
  });
});

// Or using async/await with `.rejects`.
it('tests error with async/await and rejects', async () => {
  expect.assertions(1);
  await expect(user.getUserName(3)).rejects.toEqual({
    error: 'User with 3 not found.',
  });
});
```

## Manual Mocks
- Mock data를 사용하여 기능을 대체하는 데에 사용된다.
- 원격 저장소의 데이터를 실제로 가져오는 대신, 가짜 데이터를 투입할 수 있다.

### Mocking User Modules
- `__mock__` directory의 하위 directory에 module을 작성하여 정의
```js
jest.mock('./moduleName') // call
```

### Mocking Node Modules
- Mocking하려는 Module이 Node Module일 경우 `__mock__` directory는 nodee_modules 근처에 두어야 한다.(자동 Mocking)
- jest.mock('module_name')을 call할 필요가 없음

### 이런 내용들을 알고 싶었던게 아니라 일단 패스..