# 1월 18일 월요일

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
