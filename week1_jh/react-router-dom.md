# react-router-dom
## history
- 이를 통해 페이지의 기록을 알 수 있다.
- history.push(path) 를 통해 해당 url로 이동할 수 있다.
## Hooks
### useHistory
- 탐색을 위해 사용할 수 있도록 history instance에 접근할 수 있게 한다.
- BrowserRouter가 감싸고 있는 Component에서 사용해야 한다.

### useParams
- 기존 <Route>로 사용되지 않는 Component에서 match, location, history에 접근을 위해서 withRouter HoC로 감싸줘야 했지만 useParams 추가 후 `match.params` 객체에 쉽게 접근 가능
```js
export default function example({ match }){
    const { param } = match.params;
}
//위에서 아래로 간편화
import { useParams } from 'react-router';
export default function example(){
    const { param } = useParams();
}
```
### useLocation
- location 객체에 접근

### useRouteMatch
- match 객체의 값에 접근할 수 있게 해주는 hook
- <Route>의 Props(path, stric, sensitive, exact)들을 가진 객체를 받을 수 있음
- path와 현재 URL이 일치할 경우 path의 match 객체 반환 or null
- 아무 인자도 없이 호출시 withRouter HoC로 match 객체를 접근했을 때 처럼 가장 가까운 부모 <Route>의 match return
//TODO 예제 보충하기