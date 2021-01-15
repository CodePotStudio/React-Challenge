# 1주차 
* 노마드코더의 코스 중 'ReactJS로 영화 웹 서비스 만들기'를 1월 14일 목요일부터 수강하기 시작함.
* 예전에 Velopert의 Todo-list 튜토리얼을 보고 따라해본 적은 있는데, 자바스크립트에 대한 지식이 부족할 때라서 오히려 헷갈리고 별로 기억이 없었음. 
그래서 이번에 노마드코더 강의를 들으면서 기초부터 차근히 익히겠다고 다짐함. 

## Component, props, object 
```
import React from "react";

function Food({name, picture}) {
  return <div><h2>I love {name} </h2>
         <img src = {picture} /></div>;
}

const foodILike = [
  {
    id: 1,   //각 원소가 다르다는 걸 보여주기 위해 key가 필요함
    name: "Kimchi",
    image:
      "http://aeriskitchen.com/wp-content/uploads/2008/09/kimchi_bokkeumbap_02-.jpg"
  },
  {
    id: 2,
    name: "Samgyeopsal",
    image:
      "https://3.bp.blogspot.com/-hKwIBxIVcQw/WfsewX3fhJI/AAAAAAAAALk/yHxnxFXcfx4ZKSfHS_RQNKjw3bAC03AnACLcBGAs/s400/DSC07624.jpg"
  },
  {
    id: 3,
    name: "Bibimbap",
    image:
      "http://cdn-image.myrecipes.com/sites/default/files/styles/4_3_horizontal_-_1200x900/public/image/recipes/ck/12/03/bibimbop-ck-x.jpg?itok=RoXlp6Xb"
  },
  {
    id: 4,
    name: "Doncasu",
    image:
      "https://s3-media3.fl.yelpcdn.com/bphoto/7F9eTTQ_yxaWIRytAu5feA/ls.jpg"
  },
  {
    id: 5,
    name: "Kimbap",
    image:
      "http://cdn2.koreanbapsang.com/wp-content/uploads/2012/05/DSC_1238r-e1454170512295.jpg"
  }
];

function App() {
  return (
    <div>
      {foodILike.map(dish => 
        <Food key={dish.id} name={dish.name} picture={dish.image} />
        )}      
//그래서 그 key를 여기서, react app 실행할 때 써먹는 것
    </div>
  );
}

export default App;
```
* 함수 컴포넌트와 클래스 컴포넌트가 있는데, 자바스크립트 함수 쓰는 것처럼 저렇게 함수를 정의하면 되는 모양이다. 
그래서 그 인자로 받은 props를 어떻게 할 건지 저 함수 컴포넌트 안에서 정의해주면 되는 듯.
* 저렇게 데이터가 들어있는 배열이 있으면, 그걸 map을 이용해서 함수 컴포넌트를 만드는 것 같다.
* 주의해야할 점은, 저 배열 안의 원소가 내 눈엔 다 달라보일지라도, 리액트한테는 특별히 key를 통해서 저게 다 다른 애라는 걸 알려줄 필요가 있는 것.

## prop-types
* 자바처럼 타입이 처음 지정한거랑 다르다고 해서 컴파일 오류 띄우는 것도 아니고, 그래서 prop type을 내가 제대로 넣은건지 아닌건지 제대로 확인 못할 때도 있을텐데, prop-tyeps는 그걸 확인할 수 있게 해준다고 한다. 
* npm i prop-types 쳐가지고 다운받고 임포트하고 써먹으면 됨.
```
Food.propTypes = {
 name: PropTypes.string.isRequired,
 picture: PropTypes.string.isRequired,
 rating: PropTypes.number.isRequired
}
```
이렇게 하면 각각의 props가 미리 지정한 타입을 갖고 있는지 확인할 수 있다. 

## State
버튼 한번 누를 때마다 숫자가 증가하거나 감소하는 걸 만든다고 쳤을 때, 그 변하는 데이터를 state안에 넣는다.
```
state = {
    count: 0 
  };
```
그리고 버튼 누를 때마다 저 state.count가 변하도록 해줘야하는데, this.state.count = + 1이런 식으로 쓰면 render도 안되고 화면에 변화도 없다. 이런식으로 state안의 값을 직접 바꾸려고 하는 건 의미도 없고 되는 게 없음. 그러므로 setState라는 걸 써준다.
```
add = () => {   //버튼 onClick할 때 실행될 함수들
   this.setState({count: this.state.count + 1});
};
```
이렇게 하면 state를 바꿀건데, 지금 현재 있는 state.count에서 1을 더해서 state를 set하라는 뜻이 됨. 근데 자꾸 저렇게 this.state 쓰는게 귀찮으면 current로 시작하는 함수를 써도 된다고 함.
```
this.setState(current => ({count: current.count + 1}));
```
저렇게 add랑 minus 라는 걸 써놓고, render안에다가 이제 화면에 보여질 것들이랑 버튼 누를 때마다 무슨 일이 일어날 건지 써야함.
```
  render(){
    return <div>
      <h1>The number is {this.state.count}</h1>
      <button onClick={this.add}>Add</button>
      <button onClick={this.minus}>Minus</button>
      </div>
  }
```
* onClick은 따로 구현할 필요없이 이미 button안에 있는 props라고 한다.
* this.add()라고 쓰면 바로 실행까지 하는거고, 저렇게 괄호없이 쓰면 this.add는 앞으로 버튼을 누를 때마다 실행할 함수를 '저장'만 하는 정도인 것 같다. 

## Class component
* State가 등장하긴 전까지는 예시를 들 때 function App() 이렇게 썼었는데, state가 등장하고 나서부터 강의에 class App() 이라고 쓰기 시작했다. class 컴포넌트는 함수 컴포넌트보다 몇 가지 기능이 더 있는데, 그 중 하나가 render라서 이걸 사용할 수가 있고, 그리고 클래스에 로컬 state를 추가해서 사용할 수 있기 때문에 그런 모양이다.
* 이 부분에 대해서는 리액트 문서 https://ko.reactjs.org/docs/state-and-lifecycle.html 를 읽었는데, 여기에서도 직접 state 수정하지 말고 setState를 사용하라고 써 있었다. 

## Life cycle of a component
```
class App extends React.Component{
  state = {
    count: 0
  };
  add = () => {
    this.setState(current => ({count: current.count + 1}));
  };
  minus = () => {
    this.setState(current => ({count: current.count - 1}));
  };

  componentDidMount() {
    console.log("component rendered");
  }
  componentDidUpdate() {
    console.log("component has been updated");
  }
  componentWillUnmount() {
    console.log("Good bye");
  }

  render(){
    console.log("I'm rendering");
    return <div>
      <h1>The number is {this.state.count}</h1>
      <button onClick={this.add}>Add</button>
      <button onClick={this.minus}>Minus</button>
      </div>
  }
}
```
* 저런 식으로 추가해서 렌더링을 하기 전에 일어날 일들, 렌더링을 하고 있을 때 일어날 것, 렌더링을 다 했을 때 일어날 것, 그리고 페이지를 이동한다거나 할 때 컴포넌트를 종료할 것 등, 이렇게 컴포넌트의 라이프사이클을 제어할 수 있다고 한다.  
* 그래서 예를 들면, 로딩을 할 때 보여줄 것과, 로딩을 다 하고 나서 보여줄 것을 이걸 이용해서 지정할 수가 있다고 한다. 
```
class App extends React.Component{
  state = {
    isLoading: true,
    movies: []
  }
  componentDidMount() {
    setTimeout(() => {
      this.setState({isLoading: false});
    }, 6000);
  }
  render() {
    return <div>{this.state.isLoading ? "Loading..." : "We are ready"}</div>
  }
}
```
* 일단 렌더 안에서는 state의 isLoading이 참이면 '로딩'을 보여주고, 그게 아니면 '준비됐다'고 보여주는 걸 쓴다. 그리고 compponentDidMount에다가 타임아웃을 이용해서, 6초 후에 저 스테이트 안의 값을 바꾸도록 한다. 그렇게 해서 그 state.isLoading이 바뀌면 렌더링이 또 일어나고 we are ready로 바뀌게 되는 듯. 저 componentDidMount가 지금은 setTimeout이지만, 다른 조건으로도 바꿔서 쓸 수 있을 것 같다. 
* 그런데 저기 렌더 안에 있는 거에서 this.state.isLoading이라고 쓰는 게 귀찮으니까 다른 방법을 썼는데,
```
const {isLoading} = this.state;
    return <div>{isLoading ? "Loading..." : "We are ready"}</div>
```
뭐 이게 es6만의 마법이라고 어쩌고 했는데 그게 뭔지 잘 모르겠다. 리액트 강의 다 보고 나서 그 es6강의라는 것도 한번 들어봐야겠다.
