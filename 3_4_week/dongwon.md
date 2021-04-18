
## React에서 Modal 구현

음식점에서 세부 메뉴를 선택할 때 깔끔한 UI를 위해 모달을 이용해 구현해 봤습니다.

### 필요한 컴포넌트

1. 메뉴 컴포넌트
2. Modal 컴포넌트
3. Modal을 제외한 나머지 어둡게 처리할 컴포넌트

#### 메뉴 컴포넌트

- 먼저 메뉴 리스트 중, 하나의 메뉴를 클릭하면 Modal 창이 나오도록 Menu 컴포넌트를 구성했습니다.
- `useState`를 이용해 Modal 컴포넌트를 보여줄지 결정하는 `isVisible` 변수를 생성했고, `menu 클래스` 전체에 `onClick` 이벤트를 설정해 클릭 시 보일 수 있도록 설정했습니다.

```js
// Menu.js
const Menu = ({ menu }) => {
  const [isVisible, setIsVisible] = useState(false);

  const onSetIsVisible = (active) => {
    setIsVisible(active);
  };

  return (
    <div>
      <div className="menu" onClick={() => onSetIsVisible(true)}>
        <div className="menu-info">
          <p className="menu-title">{menu.name}</p>
          <p className="menu-price">{menu.price}원</p>
          <p className="menu-desc">{menu.description}</p>
        </div>
        <div className="menu-img">
          <img src={menu.image} alt="메뉴 사진" />
        </div>
      </div>
      <div>
        {isVisible && <BodyBlackoutStyle onSetIsVisible={onSetIsVisible} />}
        {isVisible && (
          <DetailMenuModal menuId={menu.id} setIsVisible={setIsVisible} />
        )}
      </div>
    </div>
  );
};
```

#### DetailMenuModal 컴포넌트

- 메뉴를 클릭하면 상세 메뉴를 볼 수 있는 Modal 입니다. Menu.js에서 전달 받은 `setIsVisible` 함수를 이용해 세부메뉴를 고르고 주문표에 추가 버튼을 누르면 해당 로직을 수행하고 `setIsVisible(false)` 을 통해 `isVisible` 값을 false로 설정해 Mdoal 꺼지도록 구현했습니다.

```js
// DetailMenuModal.js
const DetailMenuModal = ({ menuId, setIsVisible }) => {
  const onSubmitBtn = () => {
    setIsVisible(false);
    ....
  };

  return (
    <div className="detail-menu-modal">
      <div>// Modal 세부 내용들</div>

      <div className="detail-model-submit">
        <button onClick={() => onSubmitBtn()}>주문표에 추가</button>
      </div>
    </div>
  );
};
```

- Modal의 크기는 페이지 브라우저 `width`의 40%, `height`의 80%로 설정했습니다. 또 화면의 한 가운데에 배치하기 위해 `transform` 설정과 뒷 배경의 위쪽에 위치시키기 위해 `z-index`값을 설정했습니다.

```scss
.detail-menu-modal {
  background-color: white;
  width: 40%;
  min-width: 300px;
  max-width: 600px;
  height: 80%;

  overflow-y: auto;

  position: fixed;
  left: 50%;
  top: 50%;
  padding: 5px;
  transform: translate(-50%, -50%);
  z-index: 1011;

  ...
}
```

#### BodyBlackoutStyle 컴포넌트

- Modal이 켜졌을 때 나머지 부분을 어둡게 처리하기 위한 컴포넌트입니다.
- js 에는 어두운 부분을 클릭하면 Modal이 꺼지도록만 설정했습니다.

```js
// BodyBlackoutStyle
const BodyBlackoutStyle = ({ onSetIsVisible }) => {
  return (
    <div
      className="body-blackout-style"
      onClick={() => onSetIsVisible(false)}
    ></div>
  );
};
```

- `background-color`을 이용해 색을 설정하고, Modal의 바로 아래 위치하기 위해 `z-index` 값을 Modal의 `z-index` 값보다 낮도록 설정했습니다.

```scss
.body-blackout-style {
  width: 100%;
  height: 100%;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 1010;
  background-color: rgba(0, 0, 0, 0.65);
}
```

|                                                       ✔ 메뉴                                                       |                                                       ✔ 모달                                                       |
| :----------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------: |
| <img src="https://user-images.githubusercontent.com/59330828/115149567-adbcae00-a09f-11eb-8b3c-dfed648d54b2.PNG"/> | <img src="https://user-images.githubusercontent.com/59330828/115149584-c200ab00-a09f-11eb-8aa4-457f68b4abbb.PNG"/> |
