# React Virtual DOM

React는 렌더링 성능을 위해서 가상돔을 사용합니다.
그래서 빠른 랜더링을 위해서는 돔 변경을 최소화하는것이 좋습니다.

element들이 변경 될때마다 React Develop Tool 또는 chrom 개발자 모드에서도 변경될 경우 색으로 표시되면서 
변경된 부분만 표시되는것을 확인해 보실 수 있었습니다.

그럼 React는 어떻게 여러 복합적인 Element에 대해서 가상 돔을 통해 필요한 부분만 변경하는 과정을 보도록하겠습니다.

## React의 Virtual DOM의 Real DOM 적용 과정
먼저 button이라는 하나의 React Element가 어떤 모양인지를 상펴보겠습니다.
```
const buttonElement = (
  <button key="up-button" style={{width:'50px', height:'30px'}}>증가</button>
)
console.log(buttonElement);
```
이렇게 출력을 진행하면 아래의 모양을 볼 수 있습니다.
```
{
    type: "button", // 선언된 DOM요소
    key: "up-button",
    props: {
        children: "증가", //자식 요소
        style: {width: "50px", height: "30px"}
    },
    ref: null
}
```
### 하나의 화면을 표현하기 위해서는 여러 React Element들이 Tree로 표현됩니다.
간단한 React Element Tree를 보겠습니다.
```
<div>
    <p>Count : {count}</p>
    <button style={{width:'50px', height:'30px'}} onClick={plus}>
        증가
    </button>
</div>
```
```
type: "div"
props: {
children: [
        {
            type: "p",
            props: {
                children: ["Count :", 0],
                style: {width: "50px", height: "30px"}
            },
            ref: null
        },
        {
            type: "button",
            props: {
                children: "증가",
                onClick: () => { setCount(count + 1); },
                style: {width: "50px", height: "30px"}
            },
            ref: null
        }
    ]
}
```
요런식으로 생겼습니다.

#### 그럼 본격적으로 React가 어떻게 변경상을 감지할까요?
##### 가장 먼저 가상 돔은 React Element로 부터 만들어진다는 것을 기억해주세요! 
- React에서 데이터 변경에 의한 화면 업데이트는 Render 단계와 Commit 단계를 걷히게 됩니다. 
- Render 단계은 변경사항을 파악하는 단계이고, Commit 단계는 파악된 변경 사항을 실제돔에 반영하는 단계입니다.
- Render 단계에서 React는 가상돔을 이용해 변경사항을 파악합니다.
- React는 렌더링을 할때마다 가상돔을 만들고 비교를 합니다.

만약 여기서 증가 Button을 누르게 된다면 React Element Tree는 아래와 같이 변경이 됩니다.
```
type: "div"
props: {
children: [
        {
            type: "p",
            props: {
                children: ["Count :", 1],
                style: {width: "50px", height: "30px"}
            },
            ref: null
        },
        {
            type: "button",
            props: {
                children: "증가",
                onClick: () => { setCount(count + 1); },
                style: {width: "50px", height: "30px"}
            },
            ref: null
        }
    ]
}
```
#### 여기서 변경된 부분은 type p의 children: ["Count :", 1] count변수가 바뀌면서 변화하였습니다. 
이렇게 Render과정에서 React는 변경사항을 확인했고 이부분만을 Commit과정에서 Real DOM에 적용합니다.

### !TIP
여기서 변환된 React Element의 type이 다른 컴포넌트 함수가 아닌 모두다 String일 경우, 실제 돔을 만들 수 있습니다. 이때의 React 트리를 가상 돔이라고 합니다. 

#### 만약 상태값이 변경된다면?! 
memo로 선언된 녀석은 변경사항이 없으면 이전 가상돔을 그대로 사용하며, 나머지 비교되어서 변경된 부분을 다시 렌더하게 됩니다.

