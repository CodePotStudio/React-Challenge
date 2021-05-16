# 이번 주에 한 것
- 리액트의 class생명주기 메서드와 useEffect, useRef가 어떻게 관련이 있는지 확실히 알고 싶어서 이 부분에 대해 읽어보았습니다.
- 노마드코더 강의를 들으며 리액트 훅에 대해 더 배워보았습니다.

## useEffect
지난 주에 배운 내용은 그냥 `useEffect(실행할 함수, [dependancy])` 이렇게 썼기 때문에 unmount될때 어떻게 되는 건지 이해하기 어려웠습니다. 
```
useEffect(() => {
    mount 시 실행할 내용;
    return () => {unmount 시 실행할 것을 여기에 작성해서 cleanup}
}, [dependancy]);
```
https://velog.io/@suuhyeony/React-useRef-useEffect 에 잘 정리된 내용을 참고해서 알게 되었는데, return은 컴포넌트가 사라질 때 호출되는 것이므로, 
컴포넌트가 사라질 때 clean up하고 싶은 것들 등을 저렇게 함수를 리턴하는 형식으로 쓰면 되는 모양입니다. componentWillUnmount에 해당하는 게 이것인 것 같습니다.

## useRef
useState와 useEffect는 그냥 직관적으로 이해하기 쉬웠는데, useRef는 바로 이해하기는 조금 어려웠습니다. 
```
const useClick = (onClick) => {
  const element = useRef();
  useEffect(() => {
    if (element.current) {
      element.current.addEventListener("click", onClick);
    }
    return () => {
      if (element.current) {
      element.current.removeEventListener("click", onClick);
      }
    };
  },);
  return element;
};
```
아직도 제대로 이해한건가 긴가민가하지만... useClick은 어떤 함수를 파라미터로 받아서 그걸 실행시키게 되는데 여기서는 onClick이라는 이름으로 표현했습니다. 그리고 useRef()를 이용해서 element라는 이름으로 객체를 만드는데요, 그러고 나서 이 객체가 나중에 특정 DOM객체를 가리키도록 하는 것 같습니다. 그러고 나서 useEffect를 이용해서 '만약 지금 그 참조할 DOM객체가 있는 상황이면, 그 객체에다가 AddEventListner를 해서 그 객체를 클릭할 때마다 onClick함수가 실행되도록 여기서 설정합니다. 그리고 그 ref객체은 element를 리턴합니다. 

```
const App = () => {
  const sayHello = () => console.log("say hello");
  const title = useClick(sayHello);
  return (
    <div className="App">
      <h1 ref={title}>Hi</h1>
    </div>
  );
};
```
그리고 여기서는, 이제 앞으로 클릭할 때마다 실행될 함수인 sayHello를 만들었고, 위에서 만든 함수인 useClick을 이용해서 ref객체인 title을 만들었습니다. 이 title 안에 앞으로 뭔가 클릭하면 sayHello가 실행된다는 뭐 그런 내용이 들어있는 것인데, 이제 이걸 어떤 DOM객체가 참조할 것인지 정해야합니다. 에를 들어 <h1> 제목을 눌렀을 때 실행되도록 하고 싶으면, 저렇게 <h1 ref={title}> 이렇게 써서 참조를 넣어주면 되는 것 같습니다. 

이 일들이 어떤 순서로 일어나는 걸까 궁금했는데, 제 생각에는 우선 App에서 렌더링을 하고, 다 mount가 되면 저 useClick안에 있는 useEffect가 실행되어서 element가 참조할 DOM객체가 지금 존재하는 상태인지 확인하고, 그러고 나서 저 <h1>에다가 이벤트리스너를 넣는 순서로 일어나지 않나 싶습니다. 
    
## Alert 띄우는 걸 함수형 프로그래밍으로 해보기
useState, useEffect, useRef를 사용하지는 않아서 훅이라고 부르기는 뭐하지만, alert를 띄우는 걸 함수를 이용해 작성하는 내용이 강의에 있었습니다. 
```
const useConfirm = (message = "", callback, rejection) => {
  if(typeof callback !== "function") {
    return;
  }
  const confirmAction = () => {
    if (window.confirm(message)){
      callback();
    } else {
      rejection();
    }
  }
  return confirmAction;
}
```

```
const App = () => {
  const deleteWorld = () => console.log("Deleting the world...");
  const abort = () => console.log("Aborted")
  const confirmDelete = useConfirm("Are you sure?", deleteWorld, abort);
  return (
    <div className="App">
      <button onClick = {confirmDelete}> Delete the world </button>
    </div>
  );
};
```

## 진짜 나갈거냐고 묻는 거 띄우기
```
const usePreventLeave = () => {
  const listener = (event) => {
    event.preventDefault();
    event.returnValue = "";
  };
  const enablePrevent = () => window.addEventListener("beforeunload", listener);
  const disablePrevent = () =>
    window.removeEventListener("beforeunload", listener);
  return { enablePrevent, disablePrevent };
};

const App = () => {
  const { enablePrevent, disablePrevent } = usePreventLeave();
  return (
    <div className="App">
      <button onClick={enablePrevent}>Protect</button>
      <button onClick={disablePrevent}>Unprotect</button>
    </div>
  );
};
```
