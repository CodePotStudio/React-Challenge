# 리액트를 복습하기에 https://github.com/sudheerj/reactjs-interview-questions 여기가 괜찮은거 같아서 번역 겸 정리하려고 합니다 ~ (원문은 저기서 확인하시면 됩니다 2021-04-27 기준)


1. ### 리액트는 무엇인가요? 

    리액트는 Single Page Application (SPA) 를 만들기 위한 오픈소스 프론트엔드 라이브러리 입니다. 리액트는 웹과 모바일에서 뷰 레이어를 처리하기 위해 만들어졌습니다. 리액트는 페이스북에서 만들었으며 
    2011년에 페이스북 뉴스 피드, 2012년에 인스타그램에 적용되어 배포되었습니다. 
    
2. ### 리액트의 핵심 기능은? 
 
    * **VirtualDOM** 을 사용합니다. RealDOM 의 경우에는 처리하는데 비용이 많이 들기 때문에 이를 효율적으로 하기 위한 부분입니다. 
    * 서버 사이드 렌더링을 지원합니다. 사실 이 부분은 CRA 에서 보다 현재 NextJS 프레임워크로 많이 지원되는 것 같습니다. 
    * 단방향의 데이터 흐름을 가지고 있습니다. 
    * 재사용 가능한 UI 컴포넌트를 쉽게 만들 수 있습니다. 
    
3. ### What is JSX?

    *JSX* is a XML-like syntax extension to ECMAScript (the acronym stands for *JavaScript XML*). Basically it just provides syntactic sugar for the `React.createElement()` function, giving us expressiveness of JavaScript along with HTML like template syntax.

    In the example below text inside `<h1>` tag is returned as JavaScript function to the render function.

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


   **[⬆ Back to Top](#table-of-contents)**
    
4. ### What is the difference between Element and Component?

    An *Element* is a plain object describing what you want to appear on the screen in terms of the DOM nodes or other components. *Elements* can contain other *Elements* in their props. Creating a React element is cheap. Once an element is created, it is never mutated.

    The object representation of React Element would be as follows:

    ```javascript
    const element = React.createElement(
      'div',
      {id: 'login-btn'},
      'Login'
    )
    ```

    The above `React.createElement()` function returns an object:

    ```
    {
      type: 'div',
      props: {
        children: 'Login',
        id: 'login-btn'
      }
    }
    ```

    And finally it renders to the DOM using `ReactDOM.render()`:

    ```html
    <div id='login-btn'>Login</div>
    ```

    Whereas a **component** can be declared in several different ways. It can be a class with a `render()` method. Alternatively, in simple cases, it can be defined as a function. In either case, it takes props as an input, and returns a JSX tree as the output:

    ```javascript
    const Button = ({ onLogin }) =>
      <div id={'login-btn'} onClick={onLogin}>Login</div>
    ```

    Then JSX gets transpiled to a `React.createElement()` function tree:

    ```javascript
    const Button = ({ onLogin }) => React.createElement(
      'div',
      { id: 'login-btn', onClick: onLogin },
      'Login'
    )
    ```


   **[⬆ Back to Top](#table-of-contents)**
    
5. ### How to create components in React?

    There are two possible ways to create a component.

    1. **Function Components:** This is the simplest way to create a component. Those are pure JavaScript functions that accept props object as first parameter and return React elements:

        ```jsx harmony
        function Greeting({ message }) {
          return <h1>{`Hello, ${message}`}</h1>

        }
        ```

    2. **Class Components:** You can also use ES6 class to define a component. The above function component can be written as:

        ```jsx harmony
        class Greeting extends React.Component {
          render() {
            return <h1>{`Hello, ${this.props.message}`}</h1>
          }
        }
        ```


   **[⬆ Back to Top](#table-of-contents)**
