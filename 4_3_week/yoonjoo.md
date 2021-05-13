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

## Alert 띄우는 걸 함수형 프로그래밍으로 해보기
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
