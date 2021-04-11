# React HOOK

### 오늘은 드디어 React Hook을 공부해 보게습니다.
React HOOK은 컴포넌트에 기능을 추가할 때 사용하는 함수 입니다.
컴포넌트에 상태값을 추가하거나 자식요소에 접근하는 등에 대해서 사용할 수 있습니다.
React 16.8에 추가되어 Class형 컴포넌트 보다 Hook의 장점이 많아 요즘에는 새로운 프로젝트에서 많이 사용되어지고 있습니다.

## React의 기본 Hook

- useState : 상태값
    - 초기값을 넣어서 호출할 수 있으며, 배열을 반환하고 1: 상태값, 2: 상태값 변경 함수
    ```
        function App() {
            const [isClickedOn, setIsClickedOn] = useState(false);
            const onToggle = () => {
                setIsClickedOn(!isClickedOn);
                setIsClickedOn(!isClickedOn);
            }
            const buttonText = isClickedOn? "OFF" : "ON"
            return (
                <div>
                    <button onClick={onToggle}>
                        {buttonText}
                    </button>
                </div>
            );
        }
    ```
    - 상태값 변경 함수는 비동기이며 배치로 처리해서 한 이벤트안에 이렇게 여러개 넣어놔도 한 번만 진행됩니다.
        - 왜?와이? 상태변경을 동기를 사용하면 한번에 상태 변경으로 매번 화면을 다시 그려 성능 이슈있을 수 있습니다.
    - 상태값 변경함수에 함수를 넣으면 처리되기 직전의 값을 매개변수로 받아 원하는데로 동작합니다. 아래 코드 보기
    ```
        const onToggle = () => {
            setIsClickedOn(value => !value);
            setIsClickedOn(value => !value); // 요렇게 하면 동작 잘됩니다.? 근데 굳이 이렇게 해야할까...싶습니다.
        }
    ```
    - 리엑트에서 관리하지않는 이벤트는 배치를 진행하지 않습니다. 즉, 실행될 때마다 렌더링 될 수 있습니다.
    - 만약 객체를 이용해서 상태를 관리한다면?
        - 객체 펼치기를 이용해서 상태값을 변경해야합나다. ㅡㅡ
        - 그래서 이런 경우, useState 보단 useReducer가 더 적합하다고 합니다!
    ```
        const [news, setNews] = useState({title: '', content: ''});
        const loadNews = () => {
            setNews({...news, title:'오늘의 뉴으스', content: '없음'})
        }
    ```

- useEffect
    - 이팩트(API 호출 등...)을 실행합니다.
    - 모든 이팩트는 여기서 처리합니다.
        - 왜? 와이? 컴포넌트 렌더링 중 뭔가 실행된다면 복잡도가 증가히기때문에 렌더링 후에 진행시키도록 하기 위함입니다.
    - 첫 번쨰 매개변수로 전달된 함수는 컴포넌트가 렌더링된 후에 호출 됩니다.
    - 렌더링 결과가 DOM에 반영된 이후 비동기로 호출이 됩니다.
    - useEffect는 렌더링 이후 실행됩니다.
    - 그래서 두번째 매개변수의 배열에 바라볼 변수를 넣어주면 그것이 변화했을때만 호출하게 됩니다.
        - 마치 Vue의 watch처럼 이것을 의존성 배열이라고 합니다.
        - 그럼 의존성 배경에는 무엇을 넣어줘야하는가 useEffect안에 사용되는 컴포넌트의 지역변수, 상태, 속성, 지역함수 등등 모두 넣어줘야됩니다. (setState, 외부 함수  제외)
        - BUT!! 꼭필요한 경우에만 넣어주는것이 좋습니다.
    ```
        function News({newsId}) {
        const [news, setNews] = useState(null);
        useEffect( () => {
            loadNews(newsId).then( data => setNews(data));
        }, [newsId])
        return (
            <div>
                {!news && <span>뉴스없어요!</span>}
                {news && (
                    <>
                    <div>{news.title}</div>
                    <div>{news.content}</div>
                    </>
                )}
            </div>
        );
    }
    const news1 = {title: '비트코인 심상치 않아..', content: '비트코인 1억 향해'}
    const news2 = {title: '이더리움 무슨일인가?', content: '이더리움 1천만원 돌파'}
    const loadNews = (newsId) => {
        return new Promise(response => {
            setTimeout(() => {
                response(newsId == 1? news1 : news2);
            },150)
        })
    }
    ```
    - 만약 함수를 반환한다면 unmount되기 직전 또는 다음 useEffect직전에 실행됩니다.
    ```
        useEffect( () => {
            loadNews(newsId).then( data => setNews(data));
            return () => {
                console.log('나는 unmount때 실행되요!')
            }
        }, [newsId])
    ```

## 내가 Hook을 만들어 보자
먼저 커스텀 Hook은 use로 시작해야됩니다!
만약 위의 News를 부르는 코드를 바꾼다면 파일을 하나 파서 구현해주면됩니다.
set은 반환할 필요가 없습니다. 왜? 컴포넌트에서 직접 사용안하니까요~
```
    // useNews.js
    export default function useNews({newsId}) {
        const [news, setNews] = useState(null);
        useEffect( () => {
            loadNews(newsId).then( data => setNews(data));
        }, [newsId])
        return news;
    }
```

```
    // News.js
    export default function News({newsId}) {
        const news = useNews(newsId);
    ...생략
```

이렇게 여러 동작들이 컴포넌트가 모르는 상태에서 사용할 수 있는것이 장점입니다. 코드도 간단해지고 마음도 편해지는 느낌입니다. 

## Hook 사용 규칙
1. 하나의 컴포넌트에서 훅을 호춣하는 순서는 할상 같아야 한다.
    - 즉 if문 for문안에서 사용하면 안되며, 함수안에서 선언되서도 안됩니다.
    - 즉, 조건에 따라 실행되서는 안됩니다. 쓰면쓰는것이고 안쓰면 안쓰는것이여
    - useState, useEffect에도 id값을 따로 전달하지 않기떄문에 순서정보로 처리하는데 만약이렇게 되면 뒤섞일 수 있기 때문입니다.
    - return null 같은 녀석은 항상 render와 붙혀서 씁니다.
2. 훅은 함수형 컴포넌트 또는 커스텀 훅안에서만 사용해야한다.
