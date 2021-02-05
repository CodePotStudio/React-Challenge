# react-router-dom
## history
- 이를 통해 페이지의 기록을 알 수 있다.
- history.push(path) 를 통해 해당 url로 이동할 수 있다.
## match
- <Route Path>가 어떻게 URL에 match되는 지에 대한 정보를 갖는 Object
### props
| 이름  |   자료형 |  내용 |
|------|--------|-----|
|params| object |URL로부터 파싱된 key/value쌍. path의 동적 세그먼트에 할당 |
|isExact|boolean | 전체 URL과 매칭되는지 여부|
|path | string | match에 사용되는 path pattern. Nested <Route> 생성시 유용하다. |
|url | string | URL이 match된 부분. Nested <Link> 생성시 유용하다. |
### 사용
1. Route Component : this.props.match
1. Route Render : ({match}) => ()
1. Route children : ({match}) => ()
1. withRouter : this.props.match
1. matchPath : return val
1. useRouteMatch : return val
- Route가 path를 갖지 않아 always match된다면 가장 가까운 parent match를 return

### null matches
- children의 prop를 사용하는 <Route>는 route path가 현재 Location과 맞지 않아도 children의 function을 call할 수 있다.
- 이 때 match는 null이 된다.
- 따라서 match는 일치하는 Route의 content를 렌더할 때 유용할 수 있지만 이런 상황에 이런 문제가 발생한다.
### Resolve?
- 기본적으로 relative path를 Join하여 사용하는 것으로 해결한다.
- 하지만 match가 null일 때 시도하면 Type Error가 발생함 -> 완전한 해결책은 될 수 없다는 것을 의미
```js
// relative join -> 완벽한 해결책이 아님
let path = `${match.url}/relative-path`;
```
### Pathless Route
- <Route> 내에 null match Object를 생성하는 pathless <Route>를 사용할 때 비슷한 상황이 발생한다.
```javascript
// location.pathname = '/matches'
<Route path="/does-not-match"
  children={({ match }) => (
    // match === null
    <Route
      render={({ match: pathlessMatch }) => (
        // pathlessMatch === ??? => null이 된다.
      )}
    />
  )}
/>
```
- Pathless <Route>는 `parent의 match object를 상속`하며, `parent의 match object가 null이면 null이다.`
- 따라서, parent가 null이면 이를 해결할 수 있는 parent가 없기 때문에, child route/link는 absolute path를 가져야 한다.
- 또한, parent match가 null이 될 수 있는 Pathless route는 render를 위해 `children prop를 사용할 것이다.`

## Hooks
### useHistory
- 탐색을 위해 사용할 수 있도록 history instance에 접근할 수 있게 한다.
- BrowserRouter가 감싸고 있는 Component에서 사용해야 한다.

### useParams
- k-v 쌍의 URL parameter Object를 return하는 hook
- 현재 <Route>의 match.params에 접근하는 데에 사용된다.
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
- 현재 URL을 의미하는 location object를 return하는 hook
- URL이 변경될 때 마다 새로운 값을 return하는 useState처럼 생각하면 된다.

### useRouteMatch
- `match` 객체의 값에 접근할 수 있게 해주는 hook
- <Route>의 Props(path, stric, sensitive, exact)들을 가진 객체를 받을 수 있음
- path와 현재 URL이 일치할 경우 path의 match 객체 반환 or null
- 아무 인자도 없이 호출시 withRouter HoC로 match 객체를 접근했을 때 처럼 가장 가까운 부모 <Route>의 match return
- 실제 <Route>의 Rendering 없이 Match data에 접근해서 가져올 수 있어서 유용하다.
- argument가 없을 경우 현재 <Route> match object return
- 1개의 argument를 가지며 `matchPath의 props argument와 동일할 경우` pathname(string)이나 Route가 허용하는 props를 갖는 object가 될 수 있다.
```javascript
//Route 사용시
import { Route } from "react-router-dom";

function BlogPost() {
  return (
    <Route
      path="/blog/:slug"
      render={({ match }) => {
    
        return <div />;
      }}
    />
  );
}
// useRouteMatch 사용시
import { useRouteMatch } from "react-router-dom";

function BlogPost() {
  let match = useRouteMatch("/blog/:slug");

  return <div />;
}
```