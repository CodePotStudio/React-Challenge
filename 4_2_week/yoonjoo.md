## 이번 주에 한 일
리액트 훅에 대한 노마드코더 강의를 마저 듣고 따라해보았습니다. https://nomadcoders.co/react-hooks-introduction/lectures/1593

## useState 연습
버튼을 눌렀을 때 그 버튼에 해당하는 정보를 가져와서 화면에 띄우는 걸 해봤습니다.
예를 들어서 버튼1을 누르면 텍스트에 '섹션1'에 해당하는 내용이 나오고, 버튼2를 누르면 그거에 해당하는 텍스트로 바뀌게 하는 것입니다. 

우선 각 섹션의 이름과 내용을 배열로 저장했습니다. 
```
const content = [
  {
    tab: "Section 1",
    content: "I'm the content of the Section 1"
  },
  {
    tab: "Section 2",
    content: "I'm the content of the Section 2"
  }
]
```

그런 다음에 useTabs라는 훅을 만들었습니다. 
```
const useTabs= (initialTab, allTabs) => {
  const [currentIndex, setCurrentIndex] = useState(initialTab);
  if(!allTabs || !Array.isArray(allTabs)){
    return;
  }  
  return {
    currentItem: allTabs[currentIndex],
    changeItem: setCurrentIndex
  };
}
```
우선, useTabs는 두 개의 파라미터를 가지는데 하나는 초기 인덱스이고 하나는 모든 섹션 정보가 담긴 배열입니다. 
useState를 이용해서 currentIndex라는 상태를 만들고, 그 currentIndex를 바꿀 수 있는 함수인 setCurrentindex를 얻습니다. 
혹시라도 content배열이 없거나 배열이 아닐 수도 있으니 체크를 하고, 배일이 맞는 걸 확인 한 후 currentItem과 changeItem을 리턴합니다. 
currentItem은 현재 배열에서 현재 인덱스에 담긴 아이템이고, changeItem은 현재 인덱스를 바꿀 수 있는 함수입니다. 

```
 const App = () => {
  const {currentItem, changeItem}= useTabs(0, content);
  return (
    <div className="App">
      {content.map((section, index) => (
      <button onClick={() => changeItem(index)}>{section.tab}</button>
      ))}
    <div>{currentItem.content}</div>
    </div>
  );
 };
```
useTabs를 이용해서 초기 인덱스를 0으로 했고 content[0]에 해당하는 currentItem을 가지고 왔습니다. 그리고 인덱스를 바꿀 수 있는 changeItem도 얻었고요. 
이걸 이용해서 각 content 값에 해당하는 버튼을 만들고, 각각 버튼에 changeItem(index)를 onClick으로 넣어줍니다. 그러면 버튼을 누를 때마다 인덱스 값이 바뀔거고, 상태가 변했으니 리렌더링이 됩니다.

## useEffect
이때까지는 useState를 이용해 뭘 해보는 걸 했고, 이제 useEffect에 대해 배웠습니다. 리액트 튜토리얼(https://ko.reactjs.org/docs/hooks-effect.html)에도 "리액트의 class생명주기 메서드에 친숙하다면, useEffect Hook을 componentDidMount와 componentDidUpdate, componentWillUnmount가 합쳐진 것으로 생각해도 좋습니다."라고 써있네요. 
```
const App = () => {
  const sayHello = () => console.log("hello!");
  const [number, setNumber] = useState(0);
  const [aNumber, setAnumber] = useState(0);
  useEffect(sayHello, [number]);
  return (
    <div className="App">
      <div>Hi</div>
      <button onClick={() => setNumber(number + 1)}>{number}</button>
      <button onClick={() => setAnumber(number + 1)}>{aNumber}</button>
    </div>
  );
};
```
number를 바꾸는 버튼과 aNumber를 바꾸는 버튼을 각각 만들어놨고, useEffect를 이용해 콘솔에 hello가 찍히게 했습니다. 
`useEffect(sayHello);`라고 썼을 때에는 버튼을 누를 때마다 hello가 출력이 되었는데, `useEffect(sayHello, [number]);`라고 쓰니 number가 바뀔 때에만 hello가 출력이 되었습니다. 이렇게 useEffect를 사용할 때에는 []안에다가 dependency를 적어줄 수 있다고 하네요. 

### useTitle
useEffect를 이용해서 홈페이지의 title을 바꾸는 useTitle을 만들었습니다. 
```
const useTitle = (initialTitle) => {
  const [title, setTitle] = useState(initialTitle);
  const updateTitle = () => {
    const htmlTitle = document.querySelector("title");
    htmlTitle.innerText = title;
  };
  useEffect(updateTitle, [title]);
  return setTitle;
}
```
일단 파라미터로 초기 타이트틀 받아 useState를 이용해 title 상태를 설정하고요, document에서 title을 찾아서 바꿔주는 updateTitle이라는 function을 만들었고, 
useEffect(함수, dependency) 이렇게 해주었습니다. 그러면 title이 setTitle에 의해 언젠가 바뀌게 되면 저 useEffect가 updateTitle을 이용해 페이지의 title을 바뀌게 될 것입니다. 그리고 이 useTitle은 상태를 바꾸는 함수인 setTitle을 리턴합니다.

```
const App = () => {
  const titleUpdater = useTitle("Loading...");
  setTimeout(() => titleUpdater("Home"), 5000);
  return (
    <div className="App">
      <div>Hi</div>
    </div>
  );
};
```
useTitle을 이용해서 초기 타이틀을 "Loading..."으로 설정했고, 그 상태를 바꿀 수 있는 함수인 titleUpdater가 생겼습니다. 여기에 보이지는 않지만 이제 titleUpdater를 이용해 타이틀을 바꾸게 되면, useEffect 때문에 브라우저에 타이틀이 다르게 표시가 될 것입니다. 
그걸 확인하기 위해 5초 후에 타이틀을 home으로 바꾸도록 했고, 잘 동작하는 걸 볼 수 있었습니다. 

## useEffect에 대한 내용 정리
https://ko.reactjs.org/docs/hooks-reference.html 에 있는 내용을 다시 찬찬히 읽어보았습니다. 
```
useEffect(didUpdate);
```
useEffect에 전달된 함수는 화면에 렌더링이 완료된 후에 수행이 됩니다. 즉, 위에서 한거는 setTitle을 이용해 title이라는 상태를 변경했고, 그렇게 해서 화면이 렌더링이 되었고, 그 후에 updateTitle 함수가 실행되어서 브라우저의 title을 바꾸게 된 모양입니다. 브라우저 화면이 다 그려질때까지 지연되었다가 effect가 발생하고, 그리고 다음 새로운 렌더링이 발생하기 전에 일어나는 것도 보장된다고 합니다.

`useEffect(updateTitle, [title]);` 여기서 []안에 있는 값이 변경될때만 effect가 발생하게 될 것인데요, 이렇듯 effect가 한번만 실행되게 하려면 빈 배열을 전달해서 "어느 값에도 의존하지 않으니 다시 실행할 필요 없다"고 알려주면 된다고 합니다. 

## 다음 주에 할일
이번 주에 노마드코더 리액트 훅 강의를 다 보는 게 목표였는데 바빠서 몇 개 못봤네요ㅠㅠ 다음 주에 다 보는 게 목표입니다. 
