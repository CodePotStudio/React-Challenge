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

---

#### Reference

https://ts.chibicode.com/todo/