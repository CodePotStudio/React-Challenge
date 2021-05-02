# 1주차

### useEffect와 React Lifecycle
 - React에서의 컴포넌트는 클래스 컴포넌트, 함수 컴포넌트로 나눌 수 있습니다.
 - 둘의 차이는 Lifecycle method를 사용할 수 있냐 없냐 입니다.
 - 과거 함수 컴포넌트에서는 Lifecycle method를 사용할 수 없었지만, 현재 useEffect hook을 이용하여 함수 컴포넌트에서도 Lifecycle method의 기능을 비슷하게 구현할 수 있게 되었습니다.

 ### Lifecycle
 - Lifcecycle은 크게 3가지로 나뉩니다.
 - 화면에 생성되는 Mounting
 - 업데이트되는 Updating
 - 화면에서 사라지는 Unmount
 - Lifecycle method는 클래스 컴포넌트에서만 사용합니다.

### useEffect
 - useEffect hook을 이용하여 함수 컴포넌트에서도 Mounting, Updating, Unmount를 할 수 있습니다.
 ```tsx
 useEffect(() => {
     /* 
        1. Mounting 
        2. Updating 
    */
     return () => {
         /* 3. Unmount */
     }
 }, [/* dependency 배열 */]);
 ```
 1. 컴포넌트가 화면에 나타날 때(Mounting)의 코드를 작성합니다.
 2. 컴포넌트가 업데이트 될 때의 코드를 작성합니다, dependency 배열에 업데이트 되는 상태를 작성합니다. ( *dependency배열에 상태, props 등을 작성하는 것이 규칙이며, 작성하지 않을 경우 상태가 업데이터 되야될 때 의도대로 작동하지 않을 수 있습니다.)
 3. 컴포넌트가 화면에서 사라질 때(UnMount)의 코드를 작성합니다.

### useEffect의 dependency
 - React로 회원가입 시나리오를 구현해 볼 때 useEffect의 dependency 설정때 이슈가 있었습니다.
 - 회원가입 시나리오에서 컴포넌트 구성을 다음과 같은 형태로 진행 하였습니다.
 - 부모컴포넌트 : 회원가입 컴포넌트
 - 자식컴포넌트 : email입력 컴포넌트, nickname입력 컴포넌트, password입력 컴포넌트, password확인 컴포넌트
 - 이슈가 있었던 부분은 password관련 컴포넌트 입니다.
<img src="/myeongjun/pw-component.png" width="450px" height="300px" title="pw-components" alt="pw-components"></img><br/>
 - 위의 사진과 같이 컴포넌트를 구성하였습니다.
