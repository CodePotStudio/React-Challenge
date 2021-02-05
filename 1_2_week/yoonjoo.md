## API에서 데이터를 받아오는 것
* 우선 axios를 설치해서 그걸 이용하도록 한다. 비동기방식으로 HTTP데이터 요청을 하는 녀석이라고 한다.
* `componentDidMount`안에다가 axios.get(url)을 써서 불러올 수도 있겠지만, 그러지 않고 따로 function을 만들도록 한다.
```
  getMovies = async () => {
    const movies = await axios.get("https://yts-proxy.nomadcoders1.now.sh/list_movies.json");
  }
  
   componentDidMount() {
      this.getMovies(); 
  }
```

## Async와 await
* 데이터를 불러오는 데에는 시간이 걸린다. 그러므로 비동기식으로 처리를 하는 모양이다. 
* 자바스크립트를 수박겉핥기식으로 배웠던지라 이 부분이 이해가 잘 안되어서 좀더 찾아보았다. 
우선 아주 희미하게 기억나는 프로미스에 대해서 찾아보았다. 
https://joshua1988.github.io/web-development/javascript/promise-for-beginners/
이렇게 프로미스를 이용해서 데이터를 받아오고, 그래서 데이터가 미처 다 오기도 전에 화면에 뿌리려는 일이 없도록 방지하는 것.
* 더 간단하게 쓸 수 있는 방법이 바로 이 강의에 나오는 async와 await이라고 한다.  
https://velog.io/@ejchaid/async-await 에 설명된 내용이 이해하는 데 도움이 되었는데, async와 await의 장점은 코드의 가독성이 좋아지고 에러를 찾기가 쉬워진다는 점이라고. 

## 다시 API로 돌아와서
url을 붙여넣기 할 때, 제공되는 메소드를 이용해서 데이터를 정렬을 한다거나하는 처리를 할 수 있다고 한다.
```
https://yts-proxy.nomadcoders1.now.sh/list_movies.json?sort_by=rating
```
저렇게 뒤에다가 `sort_by=rating` 이렇게 쓰면 rating 높은 순대로 보여진다거나 할 수 있다고. url긁어올 때 이런 것도 잘 챙겨서 봐야겠다. 

## 영화목록을 가져다가 컴포넌트 만드는 걸 쓴다면...
```
function Movie({ id, year, title, summary, poster }) {
    return (
        <div class="movie">
            <img src={poster} alt={title} title={title}></img>
            <div class="movie__data">
                <h3 class="movies__title">{title}</h3>
                <h5 class="movies__year">{year}</h5>
                <p class="movie__summary">{summary}</p>
            </div>
        </div>
    );
}
```
props를 저렇게 파라미터로 넣을 때, {}로 묶어줘서 저게 하나의 props라는 걸 표시해줘야하는 모양이다. 
괄호없이 그냥 id, year, title...이렇게 썼더니 되지 않았다. 
```
class App extends React.Component {
  state = {
    isLoading: true,
    movies: []  
  }

  getMovies = async () => {
    const { data: { data: { movies } } } = 
    await axios.get("https://yts-proxy.nomadcoders1.now.sh/list_movies.json?sort_by=rating");
    this.setState({ movies, isLoading: false })
  }
  componentDidMount() {
    this.getMovies();
  }
  
  render() {
    const { isLoading, movies } = this.state;

return <section class="container">
      {isLoading ? 
      (<div class="loader">
        <span class = "loader__text">"Loading..."</span>
        </div>) 
      : (<div class = "movies">
        {movies.map(movie => (
          <Movie 
          key={movie.id}  //키가 없으면 불평함
          id={movie.id} 
          year={movie.year} 
          title={movie.title} 
          summary={movie.summary} 
          poster={movie.medium_cover_image} />))
        }
        </div>)
      }</section>
  }
}
```
* 원래대로라면 this.setState({movies:movies}) 를 써서, state의 movies를 새로운 movies로 set한다고 써줘야할테지만, 
이름이 같으면 그냥 this.setState({movies}) 요렇게 써도 된다고 함. 참 똑똑하군...
* 별거 아닌 것 같지만, return할 html 태그들을 쓸 때, ()로 잘 묶어줘야되는 것 같다. 괄호 안썼더니 불평함.

## Router
* 그 전에 강좌에서 했던 거는 영화 목록을 보여주는 페이지만 만드는 거였지만, 이제 navigation을 만들어서 주소에 따라 어떤 페이지로 이동할 건지 설정해줄 수 있다. 그러려면 a가 아니라 Link를 이용해야하고, 그게 들어있는 `react-router dom` 패키지가 필요하다고. 파일이 많아져서 헷갈리니까 폴더를 두 개 만들어서 components폴더에다가는 movie와 navigation같은 파일을 넣어놓고, routes에다가는 페이지들을 띄우는 home, about, detail 같은 파일들을 넣었다. 
* 제대로 이해한 건지는 모르겠지만 a태그를 이용해서 다른 페이지로 이동을 하려고 하면 컴포넌트를 다 죽이고 새로 렌더링을 하니까, 그걸 방지하려고 Link를 대신 이용하고, Link에 있는 주소에 따라서 라우터가 해당 컴포넌트를 화면에 띄우는 식으로 이루어지는 모양. 

```
import React from "react";
import { Link } from "react-router-dom";
import "./Navigation.css";

function Navigation() {
  return (
    <div className="nav">
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
    </div>
  );
}

export default Navigation;
```
* 그래서 네비게이션 바가 들어있는 파일에 저렇게 a태그를 쓰면 안되고, 저렇게 react-router-dom에 있는 Link 를 물러다가 이용해야한다. `Link to=`라고 쓰는 것에 주의.
* 그리고 App.js에다가 라우터를 불러준다.
```
import React from "react";
import {HashRouter, Route} from "react-router-dom";
import About from "./routes/About";
import Home from "./routes/Home";
import Navigation from "./components/Navigation";
import "./App.css";

function App() {
  return (<HashRouter>
    <Navigation />
    <Route path="/" exact={true} component={Home} />
    <Route path="/about" component={About} />
  </HashRouter>);
}

export default App;
```
* about, home 등의 파일을 import 해놓고 , `HashRouter`를 이용해서 리디렉팅
* 만약 내가 저기다가 `exact={true}`를 쓰지 않는다면, 저건 /가 들어갈 때마다 Home을 띄울 것이다. 즉, 내가 /about으로 가려고 할때면 Home도 띄우고 About도 띄울 것임. 그러므로 /만 있을 때 Home을 띄우고 싶다면 저 exact부분을 써주는 게 중요하다.
* 그래서 저 path로 이동하게 되면 해당component를 페이지에 띄우는 식으로 동작해서, 매번 새 페이지를 띄우는 번거로움(?)을 줄일 수 있다. 

## Routes 사이에 정보 보내기
* 예를 들어, 영화 목록에서 영화 하나를 선택했을 때, 내가 선택한 영화에 대한 정보를 그 페이지가 상세페이지로 보내서 그 정보를 화면에 띄우는 식으로 할 수 있다. 
* 그래서 movie.js 에다가도 Link를 추가해서, 포스터 누르면 해당 영화 페이지로 이동할 수 있게 해준다. 이럴 때 id를 이용하면 좋음
```
 <Link
        to={{
          pathname: `/movie/${id}`,
          state: {
            year,
            title,
            summary,
            poster,
            genres
          }
        }}
      >
```
이렇게 Link를 통해 각종 정보가 담긴 state를 다음 페이지를 보낼 수 있다. 그리고 저 /movieid로 이동할 때 어떤 컴포넌트를 띄울 건지 app.js에다가도 추가를 해준다.
```
<Route path="/movie/:id" component={Detail} />
```
그리고 나서 detail에서 저 전달받은 state를 가지고 뭘 할건지 정해준다. 
```
import React from "react";

class Detail extends React.Component {
  componentDidMount() {
    const { location, history } = this.props;
    if (location.state === undefined) {
      history.push("/");
    }
  }
  render() {
    const { location } = this.props;
    if (location.state) {
      return <span>{location.state.title}</span>;
    } else {
      return null;
    }
  }
}
export default Detail;
```
* 만약 내가 어디서부터 리디렉트하는 입장이 아니라 주소창에 직접 주소를 /movie/id 이런식으로 치는 입장이면 전달받은 state가 없으니까 띄울 게 없다. 그러므로 state가 없을 경우 뭘 할건지도 여기다 써줘야 한다. 그래서 일단 render에서 location.state가 가진 title을 띄우고, 만약 그게 없으면 null 하고, render끝나면 componentDidMount가 location.state가 있는지 없는지 확인해서 없으면 다시 홈으로 보내버리는 듯.

## github 페이지로 deploy하기
* gh-pages를 설치해서 github 페이지에 이 홈페이지를 올리고 띄울 수 있다. 
* package.json에 scripts 항목에다가 deploy 를 추가한다. 그리고 predeploy도 추가해서, npm run deploy를 하면 먼저 빌드를 하고 deploy를 하도록 설정. 그리고 파일 마지막에 홈페이지 url도 추가해주는데 내 아이디/깃허브.io/repo이름 이런 순서임에 주의. 

## 강의 완강한 소감
* 대충 리액트가 어떻게 생긴 언어이고, 어떤 식으로 작동하는지 조금 알게 된 것 같은데, 역시 직접 뭘 만들어봐야 더 잘 알 수 있을 것 같다.
* 강좌에서 했던 영화 정보 불러와서 띄우는 식의 사이트 비슷하게, 뭔가 정보를 불러와서 화면에 띄우는 걸 '스스로' 연습해봐야할 것 같다. 다음 주에 엄청 바쁠 예정이라 얼마나 할 수 있을지는 모르겠지만, 일단... 배운 걸 잘 소화시키고 연습해보는 것이 다음 주 목표. 
