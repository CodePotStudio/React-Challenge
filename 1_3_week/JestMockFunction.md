# JestMockFunction
- 아래와 같은 forEach Function의 테스트를 위해
```js
function forEach(items, callback) {
  for (let index = 0; index < items.length; index++) {
    callback(items[index]);
  }
}
```
- 아래와 같은 mock function이 필요하다.
```js
const mockCallback = jest.fn(x => 42 + x);
forEach([0, 1], mockCallback);

// The mock function is called twice
expect(mockCallback.mock.calls.length).toBe(2);

// The first argument of the first call to the function was 0
expect(mockCallback.mock.calls[0][0]).toBe(0);

// The first argument of the second call to the function was 1
expect(mockCallback.mock.calls[1][0]).toBe(1);

// The return value of the first call to the function was 42
expect(mockCallback.mock.results[0].value).toBe(42);
```
- 위 처럼 mock function을 사용할 수 있고, mock state 상태를 검사하여 callback이 제대로 호출되는지 확인 가능

## .mock property
- 모든 Mock function은 함수 호출 방법, return된 함수에 대한 데이터가 유지되는 .mock property를 가진다.
- 또한, 각 call 마다 this의 value를 추적하는 property도 갖는다.
```js
const myMock = jest.fn();

const a = new myMock();
const b = {};
const bound = myMock.bind(b);
bound();

console.log(myMock.mock.instances);
// > [ <a>, <b> ]
```

## Mock Return Values
- Mock 함수를 사용하여 Test 중 임시 값을 주입할 수 있다.
```js
const myMock = jest.fn();
console.log(myMock());
// > undefined

myMock.mockReturnValueOnce(10).mockReturnValueOnce('x').mockReturnValue(true);

console.log(myMock(), myMock(), myMock(), myMock());
// > 10, 'x', true, true
```
- 아래처럼 functional한 style에서도 효과적
```js
const filterTestFn = jest.fn();

// Make the mock return `true` for the first call,
// and `false` for the second call
filterTestFn.mockReturnValueOnce(true).mockReturnValueOnce(false);

const result = [11, 12].filter(num => filterTestFn(num));

console.log(result);
// > [11]
console.log(filterTestFn.mock.calls[0][0]); // 11
console.log(filterTestFn.mock.calls[0][1]); // 12
```
### 다음은 useXX Test 찾기