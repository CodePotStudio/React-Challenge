## IntersectionObserver 이용한 Infinity Scroll

SPA에서 자주 사용하는 Infinity Scroll을 IntersectionObserver로 구현한 과정입니다.

### 1. Infinity Scroll를 구현하기 전에 필요한 것들

- Infinity Scroll은 페이지를 클릭하면 다음 페이지 주소로 이동하는 pagination과 달리 말 그대로 **무한하게 데이터의 끝까지 스크롤**이 가능하게 하는 기능입니다.
- 이를 구현하기 위해 특정 이벤트, 예를 들어 페이지의 하단에 도달하면 추가 데이터가 로드되도록 **이벤트를 설정**하고 정해진 데이터의 개수만큼 **추가로 로드되는 데이터**가 준비되어야 합니다.
- 지금 프로젝트의 상태에선 백엔드단에서 특정 개수만큼 데이터를 전달하는 것이 아닌 전체 데이터를 한 번에 전달해 주기 때문에 UX를 위한 Infinity Scroll은 아니지만 비슷하게 구현하기 위해 전달받은 **전체 데이터를 쪼개서 특정 개수만큼 로드**하는 방식으로 구현했습니다.

```javascript
// testPage 컴포넌트
let startNum = 0;

const TestPage = () => {
  1. const reduxData = useSelector((state) => state.data.stores);

  const [count, setCount] = useState(0);
  const [stores, setStores] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true);

    const fetchData = () => {
      try {
        2. let seperatedData = reduxData.slice(startNum, startNum + 3);

        3. startNum += 3;

        4. setStores((prev) => [...prev, ...seperatedData]);
      } catch (e) {
        console.log(e);
      }
    };

    if (reduxData !== null) fetchData();

    5. setLoading(false);
  }, [count, reduxData]);

  if (stores === []) {
    return <div>로딩 중..</div>;
  }

  return (
    <div>
      <h1>테스트</h1>
      <StoreBuild loading={loading} stores={stores} />
      <FetchBuild loading={loading} setCount={setCount} />
    </div>
  );
};
```

    1. 현재 Redux를 이용해 전체 데이터를 useSelector를 이용하여 불러옵니다.
    2. 배열 메소드 slice를 이용하여 reduxData의 0번 ~ 3번 데이터를 나눴습니다.
    3. startNum +3을 하면서 다음 추가 데이터 로드때 불러올 배열 설정,
    4. 현재 화면에 표시할 데이터들을 기존 데이터에 추가될 데이터를 합치는 과정입니다.
    5. 여기서 loading은 추가로 데이터를 불러올 때 지연될 시간을 고려한 것입니다.

### 2. 데이터를 표시할 StoreBuild 컴포넌트

- 데이터가 추가되면 이전 데이터와 합쳐진 stores를 화면에 표시할 컴포넌트입니다.

```javascript
const StoreBuild = ({ loading, stores }) => {
  return (
    <div className="allStores">
      1. {loading && "로딩 중"}
      {!loading &&
        stores &&
        stores.map((store) => (
          <div className="item-card" key={store.id}>
            <Link to={`/detail/${store.id}`}>
              <div className="card-img"></div>
              <div className="card-text">
                <div className="card-name">{store.name}</div>
                <div className="card-detail">
                  <AiFillStar />
                  <p>
                    {store.grade} ({store.feedNum}) · 배달비{" "}
                    {store.deliveryCost}원
                  </p>
                </div>
              </div>
            </Link>
          </div>
        ))}
    </div>
  );
};
```

### 3. 특정 이벤트가 적용됐을 때 추가 데이터 로드하는 FetchBuild 컴포넌트

- 페이지의 하단에 이동했을 때 이벤트가 발생하도록 구현한 컴포넌트입니다. 이 기능을 구현하기 위해 **IntersectionObserver**를 사용했습니다.
- **IntersectionObserver**는 간단하게 말해서 관찰하고자 하는 요소가 설정한 구역에 들어왔는지를 알려줍니다.

```javascript
const FetchBuild = ({ loading, setCount }) => {
  1. const fetchMoreTrigger = useRef(null);
  2. const fetchMoreObserver = new IntersectionObserver(([{ isIntersecting }]) => {
    if (isIntersecting) setCount((prev) => prev + 1);
  });

  3. useEffect(() => {
    fetchMoreObserver.observe(fetchMoreTrigger.current);
    return () => {
      fetchMoreObserver.unobserve(fetchMoreTrigger.current);
    };
  }, []);

  return (
    <div
      id="fetchMore"
      className={loading ? "loading" : ""}
      1. ref={fetchMoreTrigger}
    />
  );
};
```

    1. 기본적으로 FetchBuild 컴포넌트는 StoreBuild 컴포넌트 밑에 위치하여 렌더링 할 <div> 요소는 페이지의 맨 아래에 위치하고 있습니다. 이 요소에 ref를 설정하여 DOM을 직접 접근했습니다.
    2. isIntersecting을 이용하여 관찰하고자 하는 요소가 설정한 구역(여기선 default 구역)에 들어왔는지 여부를 확인할 수 있습니다.
      isIntersecting이 true 일 때 count 값을 변화시켜 상위 컴포넌트에서 재렌더링, 데이터를 추가로 로드할 수 있도록 설정했습니다.
    3. useEffect의 뒷정리 함수를 이용해 이벤트를 제거했습니다.

### 추가

- Infinity Scroll을 공부해보면서 지금 구현한 기능은 가장 기본적인 Infinity Scroll 기능이라고 생각됩니다. 예를 들어 특정 데이터에 접근하고 다시 뒤로 가기를 눌렀을 때 기존에 추가된 데이터가 모두 사라지는 문제같이 추가로 구현할 내용이 많다고 생각합니다.
- Scroll Event의 throttle, debounce 문제를 쉽게 해결하기 위해 IntersectionObserver를 써봤지만, 관찰할 구역 설정에 애매한 것이 많다고 생각됩니다. 얘들 들어 설정한 구역 안에 두 가지 요소의 isIntersecting가 true가 될 경우, 구역을 재설정해야 하는데 그 기준에 대해서 더 공부할 필요가 있다고 생각합니다.
