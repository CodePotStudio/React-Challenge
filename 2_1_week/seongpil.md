# Todo App: React with Typescript - 1

## Section 1: Types, Read-only Properties, and Mapped Types

### Transform data into UI

React / Vue 모두 근본적으로 __data를 UI로 변환하는 작업을__ 수행합니다.

### `ToggleTodo` 구현

- `todo` 객체에서 `toggleTodo()`를 호출하면
- 반대의 `done` boolean값을 가진 새로운 `todo`객체를 리턴합니다.

```javascript
function toggleTodo(todo) {
    return {
        text: toxo.text,
        done: !todo.done,
    }
}
```
목적을 이룬 것 처럼 보이지만, __큰 실수를__ 저질렀습니다. `id`가 사라졌네요.

이런 실수를 방지하기 위해 `typescript`가 등장합니다.

### Type checking

```typescript
type Todo {
    id: number
    text: string
    done: boolean
}
```
`Todo`객체의 type을 정의했습니다. 이를 사용해보겠습니다.

```typescript
const bar: Todo = {
    text: '...',
    done: true,
};
```

컴파일 결과는 __실패입니다.__ 원했던 대로 실패를 방지할 수 있게 됐습니다.

> 숙련된 FE개발자라도 간혹 저지를 수 있는 실수입니다. 저도 현업에서 이런 자잘한 버그를 무수히 만들어냈던 기억이 떠오르네요. 어서 빨리 Typescript를 잘다루고 싶습니다.

### Using Typescript for `toggleTodo()`

```typescript
function toggleTodo(todo: Todo): Todo {
    return {
        text: todo.text,
        done: !todo.done,
    }
}
// 컴파일 실패!

function toggleTodo(todo: Todo): Todo {
    return {
        id: todo.id,
        text: todo.text,
        done: !todo.done,
    }
}
// 컴파일 성공!
```

### Bad refactoring

```typescript
function toggleTodo(todo: Todo): Todo {
    todo.done = !todo.done;
    return todo;
}
```
컴파일은 성공했지만 원본 객체를 변경했습니다. 이게 왜 나쁘냐구요?
(요구사항과도 맞지 않습니다)

```typescript
const todo = {
    id: 1,
    text: '...',
    done; true,
};

toggleTodo(todo)
```
런타임 중 `todo`객체가 변경됐습니다. 지금은 한눈에 들어오지만 코드가 길어지면, 개발자는 `toggleTodo()`호출을 못보거나 봤더라도 해당 메서드가 원본 객체를 변경하리라 예상하지 못할 수도 있습니다.

버그가 발생할 확률이 증가합니다.

### `readonly` properties

함수가 input값을 수정하지 못하도록, `readonly` 키워드를 사용할 수 있습니다.

```typescript
type Todo = {
    readonly id: number
    readonly text: string
    readonly done: boolean
}

function toggleTodo(todo: Todo): Todo {
    todo.done = !todo.done;
    return todo;
}
// Cannot assign to 'done' because it is a read-only property
```

이제 원본을 변경하는 함수는 컴파일을 통과하지 못합니다.

### The `ReadOnly<...>` mapped type

더 간편하게 모든 프로퍼티를 읽기전용으로 처리할 수도 있습니다.

```typescript
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>
```

다른 방법도 있습니다. 그리고 이것은 Typescript의 또다른 기능을 시사합니다.

```typescript
type Foo = { bar: number }

type ReadOnlyFoo = Readonly<Foo>
// ReadonlyFoo is { readonly bar: number }
```

Typescript는 하나의 type에서 또 다른 type으로 변환을 허용합니다.
위처럼 사용한 것을 `mapped types`라 부릅니다. 마치 함수랑 비슷합니다.

```typescript
Required<...>
Partial<...>
// 다양한 mapped types가 있습니다
// https://www.typescriptlang.org/docs/handbook/2/mapped-types.html
```

### Wrap up

타입은 경량화/자동화된 유닛 테스트라고 볼 수 있습니다.

- 타입을 정의함으로써 함수의 입출력 값이 올바른 타입임을 확신할 수 있습니다.
- `readonly` 키워드를 통해 객체의 속성이 불변임을 확신할 수 있습니다.

서두에 언급한 React의 동작을 기억하시나요? 업데이트가 필요한 데이터와 React라는 함수가 Typescript를 만나 버그를 줄일 수 있습니다.

## Section 2: Array Types, Literal Types, and Intersection Types

Todo 앱에 '모두 완료 표시' 기능을 추가하겠습니다.

```typescript
function completeAll(todos) {
    // ...
}
```

__요구사항__

- `todo` 목록을 받고
- `done`이 모두 `true`인 목록을 리턴

구현에 들어가기 전에 __실수를 방지하기 위해__ 입출력 타입을 특정지어 봅시다.

### Adding types for `completeAll()`

```typescript
function completeAll(todos: Todo[]): Todo[] {}
```

> 처음엔 이걸로 타입정의를 마쳤다고 생각했지만 또하나 __실수할 수 있는 여지가__ 있습니다.

`todo`는 `readonly`로 불변이지만 `array`는 바뀔 수 있습니다.

```typescript
// Array에서는 mapped type이 아니라 readonly키워드를 사용합니다
function completeAll(
    todos: readonly Todo[]
): Todo[] {}

// Compile error - modifies the array
todos[0] = { id: 1, text: '...', done: true };

// Compile error - push() modifies the array
todos.push({ id: 1, text: '...', done: true });
```

_타입 정의는 아직 끝이 아닙니다_

### The `CompletedTodo` type

```typescript
type CompletedTodo = Readonly<{
    id: number
    text: string
    done: true /* !! */
}>
```

typescript에서 정확한 값을 타입으로 지정할 수 있습니다. 이를 __literal types__ 라고 부릅니다.

```typescript
const testTodo: CompletedTodo = {
    id: number
    text: string
    done: false
}
// 컴파일 실패! Type 'false' is not assignable to type 'true'.
```

이제 `completeAll()` 타입 정의를 마무리합니다.

```typescript
function completeAll(
    todos: readonly Todo[]
): CompletedTodo[] {
    // ...
}
```

### Intersection types

`Todo`와 `CompletedTodo`에 중복된 코드가 보입니다. 이를 줄이기 위해 `intersection type`을 사용해봅시다.

```typescript
type A = { a: number }
type B = { b: string }

// 위아래는 동일한 코드입니다
type AandB = A & B
type AandB = {
    a: number
    b: string
}
```

더 나아가서, 만약 이후에 기입된 타입이 첫번째 타입보다 더 구체적이라면 첫번째 타입을 덮어씁니다.

```typescript
type A = { foo: boolean }
type B = { foo: true }

// 위아래는 동일한 코드입니다
type AandB = A & B
type AandB = { foo: true }
```

`CompletedTodo`에 이를 적용해보겠습니다.

```typescript
type Todo = Readonly<{
    id: number
    text: string
    done: boolean
}>

type CompletedTodo = Todo & {
    readonly done: true
}
```

### Finally implementing `completeAll()`

```typescript
function completeAll(
    todos: readonly Todo[]
): CompletedTodo[] {
    return todos.map(todo => ({
        ...todo,
        done: true,
    }))
}
```

## Section 3: Union Types and Optional Properties

### New feature: Place tags

- 각 할일 아이템에 __선택적으로__ 아래와 같이 미리 정의된 태그를 추가할 수 있습니다.
  - `'Home'`
  - `'Work'`
- 각 할일 아이템에 유저가 정의한 커스텀 태그도 __선택적으로__ 추가할 수 있습니다.
  - `{ custom: 'Gym' }`
  - `{ custom: 'Supermarket' }`

이제 `Todo` 타입에 `Place`라는 키를 추가하고, 여기에 위 타입을 정의해야 합니다.

### Union types

```typescript
type Foo = number | string

const a: Foo = 1;
const b: Foo = 'hello'
```

요구사항 대로 `Place` 타입을 정의해봅시다.

```typescript
type Place = 'Home' | 'Work' | { custom: string }

type Todo = Readonly<{
    id: number
    text: string
    done: boolean
    place: Place
}>
```

### Optional properties

요구사항을 다시 보면 `place`는 선택사항입니다.

```typescript
type Todo = Readonly<{
    // ...
    place?: Place
}>
```

### Implementing `placeToString()`

지겹겠지만 다시 강조하자면, React는 __데이터를 UI로 변환__ 시킵니다.

`Place`데이터를 장소 레이블 UI로 변환해야합니다. 이를 위해 `palceToString()` 함수를 구현해야합니다.
- 입력값은 `Place` 타입 데이터입니다.
- 출력값은 __이모지가 포함된 문자열__ 입니다.

```typescript
function placeToString(place: Place): string {
  // ...
}
```

### Little Duckling's implementation

```typescript
type Place = 'home' | 'work' | { custom: string }

function placeToString(place: Place): string {
    if (place === 'home') {
        return ':home: Home'
    } else {
        return ':pin:' + place.custom
    }
}
```

그냥 보면 성공할것 같지만 __실패합니다.__ 컴파일러는 `else`문에서 `place`가 `work | { custom: string }`이란 것을 알고 있습니다. `work`일 경우 `custom` 프로퍼티는 존재하지 않습니다.

```typescript
function placeToString(place: Place): string {
    if (place === 'home') {
        return ':home: Home'
    } if else (place === 'work') {
        return ':briefcase: Work'
    } else {
        return ':pin:' + place.custom
    }
}
```

`union type`은 조건문에서 특히 큰 힘을 발휘합니다.
 
# Todo App: React with Typescript - 2

## Config

CRA를 이용해 React 프로젝트를 typescript로 작성하기 위한 설정은 간단합니다.

`npx create-react-app my-app --template typescript`

## Implement

> 구현은 생각보다 간단하지만, Event처리하는 부분이 생소했습니다.

```typescript
function addTodo(e: React.ChangeEvent<HTMLInputElement>): void {
    setInputTodo(e.target.value)
}

function onEnter(e: React.KeyboardEvent<HTMLInputElement>): void {
    if (e.key === 'Enter') {
        setTodos(state => {
        return [
            ...state,
            {
            id: todos.length,
            text: inputTodo,
            done: false,
            }
        ]
        });

        setInputTodo('');
    }
}
```

Event 별로 타입을 지정하고 `Generic` 설정을 해줘야 합니다.

[구현 repo](https://github.com/goohooh/todo-react-typescript)


# Typescript: Generics

아래와 같은 generics코드는 무슨 뜻인지 헷갈립니다.

```typescript
function makePair<
  F extends number | string,
  S extends boolean | F
>()
```

## Let's talk about `makeState()`

제네릭에 익술해질 동안 아래 코드를 사용하곘습니다.

```typescript
function makeState() {
  let state: number
  function getState() {
    return state
  }
  function setState(x: number) {
    state = x
  }
  return { getState, setState }
}
```

이 함수는 숫자만을 저장하고 변경할 수 있습니다. 문자열을 다루기 위해서 똑같지만 타입만 다른 함수를 정의해야 합니다.

## Challenge: Two different states

`makeState()`라는 함수는 공유하지만, 각각 생성 이후에는 숫자 혹은 문자열만 다루고 싶습니다.

```typescript
const numState = makeState()
numState.setState(1)
console.log(numState.getState()) // 1
numState.setState('hey') // error

const strState = makeState()
strState.setState('foo')
console.log(strState.getState()) // foo
strState.setState(9) // error
```

## Does this work?

```typescript
function makeState() {
    let state: number | string
    function getState() {
        return state
    }
    function setState(x: number | string) {
        state = x
    }
    return { getState, setState }
}

const numAndStrState = makeState();

numAndStrState.setState(1);
numAndStrState.setState('hello');
// no error, but we don't want it
```

이 함수는 __언제나__ 숫자 혹은 문자열을 받습니다. 우리가 원하는 게 아닙니다.

## Use generics

```typescript
function makeState<S>() {
    let state: S

    function getState() { return state }

    function setState(x: S) { state = x }

    return { getState, setState };
}
```

`<S>`를 함수 호출시 또 다른 인자값으로 생각하면 쉽습니다. 값이 아니라 __타입__ 입니다.

```typescript
const numState = makeState<number>();
numState.setState('foo') // error!

const strState = makeState<string>();
strState.setState(1) // error!
```

`makeState<S>()`를 우리는 `generic function` 이라고 부릅니다.

## Problem: You can create a boolena state!

문제가 있습니다. 불리언 타입을 제네릭 함수에 넘기면 불리언을 다룰 수 있게 됩니다. 이또한 우리가 원하는게 아닙니다.

__해결법__

```typescript
function makeState<S extends string | number>() {
    // ...
}

const booleanState = makeState<boolean>(); // error!
```

## Default type

숫자를 기본형으로 설정하고 싶으면?

```typescript
function makeState<S extends string | number = number>() {
    // ...
}
```

ES6의 default function parameter 처럼 type을 지정할 수 있습니다.

## Let's talk about `makePair`

```typescript
// 일단은 숫자를 받게 합니다
function makePair() {
    let pair: { first: number; second: number }

    function getPair() { return pair; }

    function setPair(x: number, y: number) {
        pair = {
            first: x,
            second: y
        }
    }
    return { getPair, setPair };
}
```

제네릭 함수로 바꿔봅시다.

## Generic `makePair`

```typescript
function makePair<F, S>() {
    let pair: { first: F; second: S }

    function getPair() { return pair; }

    function setPair(x: F, y: S) {
        pair = {
            first: x,
            second: y
        }
    }
    return { getPair, setPair };
}

const { getPair, setPair } = makePair<number, string>();

setPair(1, 'hi');
```

보시다시피 제네릭 함수에 타입은 여러개를 넘길 수 있습니다.

다음과 같이 응용해 볼 수 있습니다.

```typescript
function makePair<
    F extends number | string = number,
    S extends number | string = number
>() {}
```

혹은 두번째 `S` 타입에 첫번째 `F`타입을 넣을 수도 있습니다.

```typescript
function makePair<
    F extends number | string,
    S extends boolean | F
>() {}

// works
makePair<number, boolean>();
makePair<number, number>();
makePair<string, boolean>();
makePair<string, string>();

// error!
makePair<number, string>();
```

## Generic interfaces and type aliases

`let pair: { first: F, second: S }` 를 리팩토링 해봅시다. __interface__ or __type alias__ 를 이용할 수 있습니다.

### Generic interface

```typescript
interface Pair<A, B> {
    first: A
    second: B
}

function makePair<F, S>() {
    let pair: Pair<F, S>
}
```

### Generic type alias

```typescript
type Pair<A, B> = {
    first: A
    second: B
}

function makePair<F, S>() {
    let pair: Pair<F, S>
}
```

사용법은 둘 다 동일합니다.

## Generic classes

makeState를 클래스로 바꿔보겠습니다.

```typescript
class State<S>() {
    state: S

    getState() { return state; }

    setState(x: S) { state = x; }
}
```

클래스를 사용하기 위해서 생성시 타입 파라미터를 넘겨야 합니다.

```typescript
const numState = new State<number>();
```

---

#### References

https://ts.chibicode.com/todo/

https://github.com/typescript-cheatsheets/react

https://ko.reactjs.org/docs/static-type-checking.html#typescript