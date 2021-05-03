## 마우스 드래그로 좌우 스크롤 구현 (ft. React)

아이템들을 행으로 나열하고 설정해놓은 `width`를 넘어갈 때 `overflow-x: scroll`로 스크롤을 만들어 보일 수 있게 구현했습니다. 이때 스크롤 바를 움직이는 게 아닌 모바일 환경에서 옆으로 밀어서 이동하는 것처럼 마우스 드래그로 구현해봤습니다.

### 컴포넌트 준비

- Categories 컴포넌트 (외부에서 받아온 categories 아이템입니다.)

```javascript
const Categories = () => {
  return (
    <div>
      {categories && (
        <div className="categories">
          {categories.map((category) => (
            <div className="category" key={category.name}>
              <Link to={`/category/${category.text}`}>
                <div className="category-box"></div>
                <p>{category.text}</p>
              </Link>
            </div>
          ))}
        </div>
      )}
    </div>
  );
};
```

- scss

```scss
.categories {
  display: flex;
  overflow-x: scroll;
}

// scroll bar 제거 ( chrome 환경)
.categories::-webkit-scrollbar {
  display: none;
}
```

### useRef 사용하여 DOM에 직접 접근

- 좌우 슬라이드 스크롤의 움직임은 해당 DOM의 `scrollLeft`로 움직입니다.
  해당 DOM의 `scrollLeft`를 얻기 위해 `useRef`를 사용하여 DOM에 접근했습니다.

```javascript
const Categories = () => {
  const scrollRef = useRef(null);

  return (
    <div>
      {categories && (
        <div className="categories" ref={scrollRef}>
          ...
        </div>
      )}
    </div>
  );
};
```

### 마우스 클릭 이벤트와 스크롤 하는데 필요한 변수들

**사용할 이벤트**

1. `onMouseDown`
   마우스 왼쪽 버튼 누르고 있는 상태입니다.
2. `onMouseUp`
   마우스 왼쪽 버튼 뗀 상태입니다.
3. `onMouseMove`
   마우스를 움직이는 상태입니다. ( 클릭 하던 안 하던 상관없이)
4. `onMouseLeave`
   DOM에서 마우스가 벗어났는지 체크하는 이벤트입니다.

**사용할 변수**

1. `DOM.scrollWidth`
   스크롤 할 수 있는 총 길이
2. `DOM.clientWidth`
   설정한 max width ( 화면에 보이는 스크롤의 길이 )
3. `DOM.scrollLeft`
   스크롤 가장 왼쪽 (`DOM.scrollLeft = 0` ) 부터 이동한 스크롤 길이. 즉 `DOM.scrollLeft` 길이만큼 스크롤 이동
4. `mouseEvent.pageX`
   `onMouseDown`시 x 좌표.

### 코드 구현

```javascript
const Categories = () => {
  const scrollRef = useRef(null);
  1️⃣const [isDrag, setIsDrag] = useState(false);
  const [startX, setStartX] = useState();

  2️⃣const onDragStart = (e) => {
    e.preventDefault();
    setIsDrag(true);
    setStartX(e.pageX + scrollRef.current.scrollLeft);
  };

  3️⃣const onDragEnd = () => {
    setIsDrag(false);
  };

  const onDragMove = (e) => {
    4️⃣if (isDrag) {
      6️⃣ scrollRef.current.scrollLeft = startX - e.pageX;
    }
  };

return (
  <div>
    <div
      className="categories"
      onMouseDown={onDragStart}
      onMouseMove={onThrottleDragMove}
      onMouseUp={onDragEnd}
      onMouseLeave={onDragEnd}
      ref={scrollRef}
    ></div>
  </div>
);
```

1. `onMouseMove`는 왼쪽 버튼을 떼도 발생합니다. 드래그 효과를 주기 위해 `isDrag`변수가 `true` 일 때 발생하도록 설정했습니다.
2. `startX`는 현재 클릭한 `pageX`와 움직인 스크롤의 길이 `scrollLeft`를 합친 값입니다. 스크롤이 이동하지 않았을 때는 문제가 없지만 스크롤이 이동된 상태에서 클릭을 한다면, 브라우저의 `width`의 `pageX`값이 설정이 돼 순간적으로 앞쪽으로 스크롤이 됩니다. 이를 막기 위해 `scrollLeft`를 더해 현재 `x`의 위치를 계산했습니다.
3. `onMouseUp`, `onMouseLeave` 이벤트가 발생했을 때 `isDrag`를 `false`로 설정했습니다.
4. `isDrag`가 `true`일 때
5. 스크롤을 실질적으로 움직이게 하는 부분입니다. 처음 클릭한 `x`의 좌표 `startX`와 움직이면서 변하는 `e.pageX`로 `scrollLeft`의 값을 설정했습니다.

### 추가로 발생한 문제들

#### startX 위치 재조정

마우스를 움직였을 때 더 이상 스크롤을 할 수 없는 상태에서, 반대쪽으로 스크롤을 움직이고 싶을 때, 처음 마우스를 **클릭한 지점을 지나쳐야** 스크롤이 움직입니다. ( `startX`의 `scrollLeft` 값이 변하지 않기 때문에 )
이를 해결하기 위해 `DOM`의 속성들을 이용해 해결했습니다.

```javascript
const onDragMove = (e) => {
  if (isDrag) {
    const { scrollWidth, clientWidth, scrollLeft } = scrollRef.current;

    scrollRef.current.scrollLeft = startX - e.pageX;

    1️⃣if (scrollLeft === 0) {
      setStartX(e.pageX);
    } 2️⃣else if (scrollWidth <= clientWidth + scrollLeft) {
      setStartX(e.pageX + scrollLeft);
    }
  }
};
```

- 분석
  1. 가장 왼쪽일 때, 움직이고 있는 마우스의 `x`좌표가 곧 `startX`로 설정.
  2. 가장 오른쪽일 때, 움직이고 있는 마우스의 `x`좌표에 현재 스크롤된 길이 `scrollLeft`의 합으로 설정

#### 너무 많은 이벤트 발생

`onScroll` 이벤트와 같이 `onMouseMove`도 수많은 이벤트가 발생했습니다.
`Debounce` 대신 이벤트를 delay 시켜 끊어주는 `Throttle`을 사용하여 해결했습니다.
delay를 일반적인 250ms를 설정하면 너무 불편해 100ms로 설정했습니다.

```javascript
// 쓰로틀 구현
// util.js
const throttle = (func, ms) => {
  let throttled = false;
  return (...args) => {
    if (!throttled) {
      throttled = true;
      setTimeout(() => {
        func(...args);
        throttled = false;
      }, ms);
    }
  };
};
```

```javascript
// 컴포넌트
  const onDragMove = (e) => {
        ...
  };

const delay = 100;
const onThrottleDragMove = throttle(onDragMove, delay);

return(
    ...
     <div
          className="categories"
          onMouseDown={onDragStart}
          onMouseMove={onThrottleDragMove}
          onMouseUp={onDragEnd}
          onMouseLeave={onDragEnd}
          ref={scrollRef}
        >
    ...
)
```

#### 평상 시 이벤트 제거하기

`onMouseMove` 이벤트는 클릭 여부와 상관없이 해당 DOM에 위치하면 발생합니다.
이를 막기 위해 `isDrag`가 `false` 일 때 작동하지 않도록 설정했습니다.

```javascript
return(
    ...
     <div
          className="categories"
          onMouseDown={onDragStart}
          onMouseMove={isDrag ? onThrottleDragMove : null}
          onMouseUp={onDragEnd}
          onMouseLeave={onDragEnd}
          ref={scrollRef}
        >
    ...
)
```
