# Advanced

## Writable selector

지금까지 selector는 마치 observable getter처럼 쓰인 케이스만 살펴봤습니다.

```javascript
const mySelector = selector({
    key: "mySelector",
    get: ({ get }) => get(myAtom) * 100,
    set: ({ set }, newValue) => set(myAtom, newValue),
})
```

`set` 프로퍼티가 있는 selector는 __writable__ 한 상태를 리턴합니다.

양방향 selector(set 속성이 있는)는 새로운 value를 인자로 받고 이를 이용하여 data-flow graph를 따라 upstream으로 변경을 전파할 수 있습니다.

```javascript
const transformSelector = selector({
    key: "transformSelector",
    get: ({ get }) => get(myAtom) * 100,
    set: ({ set }, newValue) => set(
        myAtom,
        newValue instanceof DefaultValue ? newValue : newValue / 100
    ),
})
```

이 selector는 위 selector와 달리 값을 변경하고 있습니다. 이런 경우 새로 들어온 `newValue`가 defaultValue인지 아닌지 체크해야 합니다.(default값은 변경할 필요가 없기 때문이죠)
<[API Document 참고](https://recoiljs.org/docs/api-reference/core/selector/)>


## Asynchrounous Data Queries

우선 동기적으로 값을 핸들링하는 경우를 살펴봅시다.

```javascript
const currentUserIDState = atom({
  key: 'CurrentUserID',
  default: 1,
});

const currentUserNameState = selector({
  key: 'CurrentUserName',
  get: ({get}) => {
    return tableOfUsers[get(currentUserIDState)].name;
  },
});

function CurrentUserInfo() {
  const userName = useRecoilValue(currentUserNameState);
  return <div>{userName}</div>;
}

function MyApp() {
  return (
    <RecoilRoot>
      <CurrentUserInfo />
    </RecoilRoot>
  );
}
```

유저의 데이터가 DB에 저장돼있다면 `Promise`를 리턴하거나 `async/await`를 사용하기만 하면 됩니다. 이때 어떠한 dependency가 변경되면 selector는 새로운 비동기 요청을 만듭니다. 결과는 캐쉬되며, 비동기 요청은 유니크한 인풋당 한번식 시행됩니다.

```javascript
const currentUserNameQuery = selector({
    key: 'CurrentUserName',
    get: async ({ get }) => {
        const res = await myDBQuery({
            userID: get(currentUserIDState),
        });
        return res.name;
    }
});

function CurrentUserInfo() {
    const userName = getRecoilValue(currentUserNameQuery);
    return <div>{userName}</div>;
}
```

selector의 인터페이스는 여전히 동일합니다. 하지만, 데이터를 불러오기 전엔 어떻게 할까요? 렌더링은 동기적으로 발생하는데 말이죠...   __사실 Recoil은 [React Suspense](https://reactjs.org/docs/concurrent-mode-suspense.html)와 함께 비동기 핸들링을 처리하도록 고안됐습니다.__

`Suspense`로 컴포넌트를 감싸고 데이터를 불러오는 동안 fallback UI를 렌더링합니다.

```javascript
function MyApp() {
    return (
        <RecoilRoot>
            <React.Suspense fallback={<div>Loading...</div>}>
                <CurrentUserInfo />
            </React.Suspense>
        </RecoilRoot>
    );
}
```

## Error Handling