## 이번 주에 한 일
리액트 훅에 대한 노마드코더 강의를 마저 듣고 따라해보았습니다. 

## useTabs
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

