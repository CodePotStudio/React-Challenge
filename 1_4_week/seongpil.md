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


## Asynchrounous Data Queries