# 이벤트 처리하기

React 엘리먼트에서 이벤트를 처리하는 방식은 DOM 엘리먼트에서 이벤트를 처리하는 방식과 매우 유사하다. 

- React의 이벤트는 소문자 대신 캐멀 케이스를 사용한다.
- JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달합니다.

HTML은 다음과 같다.

```jsx
<button onclick="activateLasers()">
	Activate Laser
</button>
```

React는 아래와 같다.

```jsx
<button onclick={activateLasers}>
	Activate Laser
</button>
```

또 다른 차이는, React 에서는 `false`를 반환해도 기본 동작을 방지할 수 없습니다. 반드시 `preventDefault`를 명시적으로 호출해야만 합니다. 예를 들어, 일반 HTML에서는 새 페이지를 여는 링크의 기본 동작을 방지하기 위해 다음과 같은 코드를 작성한다.

```jsx
<a href="#" onclick="console.log('The link was clicked.'); return false">
	click me
</a>
```

React는 아래와 같다.

```jsx
function ActiveLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked');
  }
  return (
  	<a href='#' onClick={handleClick}>
  		Click me
  	</a>
  );
}
```

여기서 e는 합성 이벤트이다. React는 W3C 명세에 따라 합성 이벤트를 정의하기에 브라우저 호환성에 대해 걱정할 필요가 없다.

React를 사용할 때 DOM 엘리먼트가 생성된 후 리스너를 추가하기 위해 addEventListener 를 호출할 필요는 없다.



ES6 클래스를 이용하면 컴포넌트를 정의할 때, 일반적인 패턴은 이벤트 핸들러를 클래스의 메소드르 만드는 것이다.

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};
    
    this.handleClick = this.handleClick.bind(this);
  }
  
  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }
  
  render() {
    return (
      <button onClick={this.handleClick}>
      	{this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
	<Toggle />,
  document.getElementById('root')
)
```

js에서 클래스 메소드는 기본적으로 바인딩되어 있지 않다. this.handleClick을 바인딩 하지 않고 onClick에 전달했다면, 함수가 실제로 호출될 때 this는 undefined 가 된다.



