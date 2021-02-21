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

---

#### References

https://ts.chibicode.com/todo/

https://github.com/typescript-cheatsheets/react

https://ko.reactjs.org/docs/static-type-checking.html#typescript