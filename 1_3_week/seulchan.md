# React Challenge 3주차

이번 기회에 써보거나 정리해보고 싶었던 개념.

다음 내용들은 챌린지가 진행되면서 추가/제거될 수 있음.

- next.js
  - [x] complete next.js tutorial
  - [x] read next.js documentation
  - [ ] next.js debugging
  - [ ] simple project with next.js
- react concept
  - [ ] About useCallback
  - [ ] lifecycle
- mobx (or redux)
- others
- [ ] socket.io (chat in react)
- [ ] css tools (styled component?)
- [ ] infinite scroll & lazy loading


# mobx

## [mobx getting-started](https://mobx.js.org/getting-started)

MobX is state management solution.

### Core idea

state; 애플리케이션의 핵심. 상태를 제대로 관리하지 못하거나 동기화가 제대로 되지 않으면 버그가 많이 발생

mobx는 근본적인 문제를 해결하여 상태 관리를 단순화
- application state에서 derived 될 수 있는것들은 모두 자동으로 derived 되게 함
- 일관성 없는 상태를 생성하는것을 불가능

![image](https://mobx.js.org/assets/getting-started-assets/overview.png)

1. application state: 애플리케이션에서 정의된 모든 상태
2. derivation: application state에서 자동으로 계산될 수 있는 모든 값
3. reactions: derivation과 비슷하지만 value를 만들어내지 않고 task를 실행시킴. 보통 I/O와 관련됨. DOM이 잘 업데이트 되거나 정확한 시점에 network 요청이 보내졌는지를 확실하게 해줌.
4. actions: state를 변경시키는 모든것. MobX는 application state를 변경하는 action이 derivation, reaction에 자동으로 적용되게 함.

### Simple todo store

아래는 todo를 관리하는 간단한 TodoStore 예시

```
class TodoStore {
  todos = [];

  get completedTodosCount() {
    return this.todos.filter(
      todo => todo.completed === true
    ).length;
  }

  report() {
    if (this.todos.length === 0)
      return "<none>";
    const nextTodo = this.todos.find(todo => todo.completed === false);
    return `Next todo: "${nextTodo ? nextTodo.task : "<none>"}". ` +
      `Progress: ${this.completedTodosCount}/${this.todos.length}`;
  }

  addTodo(task) {
    this.todos.push({
      task: task,
      completed: false,
      assignee: null
    });
  }
}

const todoStore = new TodoStore();
```

todos collection을 가진 todoStore 생성.

어떻게 변경되었는지 보기 위해 변경 후 `todoStore.report`를 매번 호출하였다.

```
todoStore.addTodo("read MobX tutorial");
console.log(todoStore.report());

todoStore.addTodo("try MobX");
console.log(todoStore.report());

todoStore.todos[0].completed = true;
console.log(todoStore.report());

todoStore.todos[1].task = "try MobX in own project";
console.log(todoStore.report());

todoStore.todos[0].task = "grok MobX tutorial";
console.log(todoStore.report());
```

### Reactive

지금까지 코드는 그냥 단순한 todo store. report()를 매번 변경할 때맏 호출을 해야함.

만약 명시적으로 report를 호출하지 않고 모든 todo 상태 변경시에 호출이 되어야 한다고 선언할 수 있으면?

이를 mobx가 해줄 수 있음. 상태에만 의존하는 코드를 자동으로 실행.

즉, report function은 자동으로 업데이트 됨 (엑셀 스프레드시트의 차트처럼)

```
class ObservableTodoStore {
  todos = [];
  pendingRequests = 0;

  constructor() {
    makeObservable(this, {
      todos: observable,
      pendingRequests: observable,
      completedTodosCount: computed,
      report: computed,
      addTodo: action,
    });
    autorun(() => console.log(this.report));
  }

  get completedTodosCount() {
    return this.todos.filter(
      todo => todo.completed === true
    ).length;
  }

  get report() {
    if (this.todos.length === 0)
      return "<none>";
    const nextTodo = this.todos.find(todo => todo.completed === false);
    return `Next todo: "${nextTodo ? nextTodo.task : "<none>"}". ` +
      `Progress: ${this.completedTodosCount}/${this.todos.length}`;
  }

  addTodo(task) {
    this.todos.push({
      task: task,
      completed: false,
      assignee: null
    });
  }
}

const observableTodoStore = new ObservableTodoStore();
```

observerble을 통해 시간이 지나면서 값이 변경될 수 있음을 mobX에게 알릴 수 있음

> 요부분부터 잘 이해가 안가긴 함. makeObservable에 대해서 더 자세하게 읽어볼 필요가 있을듯.

[makeObservable 문서](https://mobx.js.org/observable-state.html#makeobservable)

### Making React reactive

> getting-started를 읽다가 막히는 부분이 많아서 mobx문서를 차근차근 읽어볼 예정.

> react에서 state management tool을 써야하는 이유를 직접적으로 느끼지 못해서 더 그런것같기도. react context api와 redux를 먼저 보고 mobx를 봐야하나 싶기도 함.


## next.js project

next.js로 간단한 토이프로젝트. 개인적으로 하나 하고, 프로젝트로 협업해서 하나 할 예정.

- 개인 프로젝트: 간단한 Todolist
- 팀프로젝트: 매일 고양이 사진 하나씩 보여주는 웹 애플리케이션

진행중에 어렵거나 정리하고 싶은 내용 정리할 예정.

### Todolist

목표:
- 간단한 프로젝트 배포까지 작성
- next.js best practice; pages, components 등 구성 익혀보기
- 깔끔한 css: flex, display, position 익히기

기능:
- 단일 페이지에서 todo CRUD 구현

(optional)
- 필요한 경우 todo 저장
- 이 경우 api 필요 -> api 서버를 따로 띄우거나 next.js api 사용
- next-auth 써서 간단한 auth 붙여보기

일정:
- 5월 말까지 배포

