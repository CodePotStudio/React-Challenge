# 리액트를 복습하기에 https://github.com/sudheerj/reactjs-interview-questions 여기가 괜찮은거 같아서 번역 겸 정리하려고 합니다 ~ (원문은 저기서 확인하시면 됩니다 2021-04-27 기준)


1. ### 리액트는 무엇인가요? 

    리액트는 Single Page Application (SPA) 를 만들기 위한 오픈소스 프론트엔드 라이브러리 입니다. 리액트는 웹과 모바일에서 뷰 레이어를 처리하기 위해 만들어졌습니다. 리액트는 페이스북에서 만들었으며 
    2011년에 페이스북 뉴스 피드, 2012년에 인스타그램에 적용되어 배포되었습니다. 
    
2. ### 리액트의 핵심 기능은? 
 
    * **VirtualDOM** 을 사용합니다. RealDOM 의 경우에는 처리하는데 비용이 많이 들기 때문에 이를 효율적으로 하기 위한 부분입니다. 
    * 서버 사이드 렌더링을 지원합니다. 사실 이 부분은 CRA 에서 보다 현재 NextJS 프레임워크로 많이 지원되는 것 같습니다. 
    * 단방향의 데이터 흐름을 가지고 있습니다. 
    * 재사용 가능한 UI 컴포넌트를 쉽게 만들 수 있습니다. 
    
3. ### JSX 란? 
 
    *JSX* 는 XML과 같은 문법 익스텐션을 ECMAScript로 변환해주는 편리한 문법입니다. 기본적으로 리액트의 `React.createElement()` 호출을 쉽게 HTML과 같이 사용할 수 있게 합니다.
    아래는 h1 태그를 jsx 로 만든 예시이고 HTML 과 매우 유사하다는 것을 알 수 있습니다. 

    ```jsx harmony
    class App extends React.Component {
      render() {
        return(
          <div>
            <h1>{'Welcome to React world!'}</h1>
          </div>
        )
      }
    }
    ```
    
4. ### Element 와 Component 의 차이점은? 

    *Element* 는 Plain Object 로 DOM 노드나 다른 컴포넌트에 어떤 걸 그리고 싶은지를 나타냅니다. *Elements* 는 다른 *Elements* 를 하위에 가질 수 있습니다. 리액트 Element 는 비용이 많이 들지 않는     동작이며 만들어진 후에는 변경되지 않습니다. 

    Element 는 아래와 같이 사용됩니다. 

    ```javascript
    const element = React.createElement(
      'div',
      {id: 'login-btn'},
      'Login'
    )
    ```

    그리고 createElement 는 아래와 같이 구성된 Object 를 반환합니다. 

    ```
    {
      type: 'div',
      props: {
        children: 'Login',
        id: 'login-btn'
      }
    }
    ```

    그 이후에 아래와 같이 ReactDOM.render() 를 이용하여서 DOM 에 렌더링되게 됩니다.     

    ```html
    <div id='login-btn'>Login</div>
    ```
    
    반면 **component** 는 몇가지 다른 방식으로 선언될 수 있습니다. Class 의 render() 또는 Function 으로 정의할 수 있으며 props 받아서 JSX 를 구성하여 반환하게 됩니다. 

    ```javascript
    const Button = ({ onLogin }) =>
      <div id={'login-btn'} onClick={onLogin}>Login</div>
    ```

    Then JSX gets transpiled to a `React.createElement()` function tree:
    JSX 는 아래와 같이 `React.createElement()` 로 트랜스컴파일 되어 사용됩니다. 

    ```javascript
    const Button = ({ onLogin }) => React.createElement(
      'div',
      { id: 'login-btn', onClick: onLogin },
      'Login'
    )
    ```

    
5. ### 리액트에서 컴포넌트를 만드는 법

    Class 방식과 Functional 방식이 있다. 

    1. **Function Components:** 는 가장 간단한 방식의 컴포넌트 구현 방법이다. Props Object 를 이용해서 React Elements 를 반환한다. 최근에는 Hooks 를 이용하여 대부분 Functional 로 많이 구현한        다.   

        ```jsx harmony
        function Greeting({ message }) {
          return <h1>{`Hello, ${message}`}</h1>

        }
        ```

    2. **Class Components:** 도 사용할 수 있다. 

        ```jsx harmony
        class Greeting extends React.Component {
          render() {
            return <h1>{`Hello, ${this.props.message}`}</h1>
          }
        }
        ```



