## 이번 주에 알게 된 것
이번 주에도 Velopert님의 블로그를 보고 공부를 조금 해봤습니다. https://velopert.com/3638

### 배열에서 데이터를 제거하는 것
```
const arr = [1, 2, 3, 4, 5];
array.slice(0,2).concat(array.slice(3,5)) // [1, 2, 4, 5]
[ ...array.slice(0,2), ...array.slice(3,5) ]; //또는
```
slice를 이용해서 배열을 저렇게 잘라서 다시 concat으로 붙이거나, 지난 주에 알게 된 전개연산자(...)를 이용해서 새 배열을 만들 수 있지만 filter를 사용할 수도 있습니다. 
```
array.filter(num => num !== 3); // [1, 2, 4, 5]
```
이렇게 하면, array안에 있는 원소를 하나씩 체크해서 3이 아닌 걸로만 새 배열을 만들게 됩니다. 
그래서 저번에 만들었던 전화번호부에서, 항목을 삭제하는 기능을 이걸로 만들 수 있습니다. 
```
class App extends Component {
  id =2 
  state = {
    information: [
      {
        id: 0,
        name: 'Alice',
        phone: '010-1234-5678'
      },
      {
        id: 1,
        name: 'Bob',
        phone: '010-2345-6789'
      }
    ]
  }
  
  handleCreate = (data) => {
    const { information } = this.state;
    this.setState({
      information: information.concat({id: this.id++, ...data})
    })
  }

  //이번 주에 새로 추가한 것
  handleRemove = (id) => {
    const {information} = this.state;
    this.setState({
      information: information.filter(info => info.id !== id)
    })
  }

  render() {
     return (
      <div>
        <PhoneForm
          onCreate={this.handleCreate} />
          <PhoneInfoList 
            data = {this.state.information} 
            onRemove = {this.handleRemove} //이 부분 추가
          />
      </div>
    )
  }
}
```
PhoneInfoList 컴포넌트를 만들 때, information뿐만 아니라 handleRemove도 함께 전달하도록 했는데, handleRemove는 information 중에서 그 id를 가지지 '않는' 것들만 모아서 새 배열을 만들고 state를 업데이트하는 역할을 합니다.
PhoneInfoList는 다음처럼 변경되었습니다.
```
class PhoneInfoList extends Component {
  static defaultProps = {
    list: [],
    onRemove: () => console.warn('onRemove not defined')   //이번 주 추가된 내용
  }

  render() {
    const { data, onRemove } = this.props; //이번 주 수정된 내용
    const list = data.map(
      info => (
        <PhoneInfo 
          key={info.id} 
          info={info}
          onRemove={onRemove} //이번 주 추가된 내용
        />)
    );

    return (
      <div>
        {list}    
      </div>
    );
  }
}
```
PhoneInfoList는 PhoneInfo를 만들 때, 전달받은 onRemove를 다시 그 컴포넌트에 전달하게 됩니다. 그러면 각 전화번호 항목에서 onRemove를 사용할 수 있게 되겠네요.
PhoneInfo는 다음과 같이 변경되었습니다.

```
class PhoneInfo extends Component {
  static defaultProps = {
    info: {
      name: 'Name',
      phone: '010-0000-0000',
      id: 0
    }
  }

  handleRemove = () => { //이번 주 수정된 내용
    const {info, onRemove} = this.props;  
    onRemove(info.id);
  }
  
  render() {
    const style = {
      border: '1px solid black',
      padding: '8px',
      margin: '8px'
    };

    const {
      name, phone
    } = this.props.info;
    
    return (
      <div style={style}>
        <div><b>{name}</b></div>
        <div>{phone}</div>
        <button onClick = {this.handleRemove}>Delete</button>  //이번 주 수정된 내용
      </div>
    );
  }
}
```
각 항목에 버튼을 추가해주고, `onClick = {this.handleRemove}`을 통해 그 메소드를 실행하도록 했네요. 
이 버튼을 눌렀을 때, 이 컴포넌트 안에 있는 handleRemove가 info와 onRemove(PhoneInfoList를 통해 App으로부터 전달받은 바로 그 handleRemove)를 실행하고,
그러면 App에 있던 그 handleRemove가 이 항목의 id를 이용해서 전화번호 리스트를 업데이트하게 됩니다. 

### 배열에서 데이터를 수정하는 것
제거하는 거는, 그냥 기존 배열에서 filter를 이용해서 해당 조건에 맞는 원소들만 골라 새 배열을 만드는 것으로 할 수 있었는데, 이번에는 그 항목을 지우지 않고 수정하기 위해 map을 사용할 수 있습니다.
```
const array = [
  { id: 0, text: 'hello', tag: 'a' },
  { id: 1, text: 'world' , tag: 'b' },
  { id: 2, text: 'bye', tag: 'c' }
];

const modifiedArray = array.map(item => item.id === 1
  ? ({ ...item,. text: 'Korea' })
  : item 
```
자바문법에 익숙한 저로서는 사실... 자바스크립트의 저 물음표 같은 것들이 아직도 좀 생소한데요. 여튼 여기서 item.id가 1과 같으면, 새로운 객체를 만들어서 id랑 tag는 그냥 기존 거랑 같게 하지만 text는 바꾸고,
id가 일치하지 않는 것은 그냥 기존의 item을 사용하도록 합니다. 저는 여기서 ...item,다음에 .를 쓰는 게 왜 그런지 잘 모르겠는데 이따가 좀더 찾아보겠습니다. 

그래서 우리 전화번호부에서도 이제 handleUpdate를 이걸 통해 해줄 수 있는데, App 클래스에다가 다음과 같이 추가했고요,
```
  handleUpdate = (id, data) => {
    const { information } = this.state;
    this.setState({
      information: information.map(
        info => id === info.id
          ? { ...info, ...data } // 새 객체를 만들어서 기존의 값과 전달받은 data 을 덮어씀
          : info // 기존의 값을 그대로 유지
      )
    })
  }
```
여기서 id와 전달받은 id가 같다면, 새로운 객체를 만들고, 기존 info에다가 data를 덮어쓰게 되고, 그게 아니라면 기존의 info를 유지하게 합니다. 

PhoneInfoList는 아까 삭제기능 만들었던 것처럼 비슷하게 수정하면됐고, PhoneInfo에 좀 쓸 게 많았는데요,
```
class PhoneInfo extends Component {
  ...
  
  state = {
    editing: false,   //원래는 텍스트로 보이는데, 이게 true일때는 수정모드(input 형태)로 바꾸기 위해 이렇게 합니다.
    name: '',
    phone: '',
  }

 ...

  handleToggleEdit = () => {  //editing값을 toggle하기 위해 사용합니다. 
    const { editing } = this.state;
    this.setState({ editing: !editing });
  }

  handleChange = (e) => {   //input에서 변화가 일어날 때 사용되는 handleChange함수입니다.
    const { name, value } = e.target;
    this.setState({
      [name]: value
    });
  }

  componentDidUpdate(prevProps, prevState) {
     const { info, onUpdate } = this.props;
    if(!prevState.editing && this.state.editing) { // 해당 항목 수정을 누르면, 
      this.setState({   
        name: info.name,   //현재 state에다가 그 info의 내용들을 넣어서 input form에 표시되도록 합니다.
        phone: info.phone
      })
    }

    if (prevState.editing && !this.state.editing) {  //수정을 다 마치고 적용을 누르면
      onUpdate(info.id, {
        name: this.state.name,   //폼에 있는 값을 state에 저장하게 됩니다.
        phone: this.state.phone
      });
    }
  }
  
  render() { 
    ...
    const { editing } = this.state;
    
    if (editing) { // 수정모드
      return (
        <div style={style}>
          <div>
            <input
              value={this.state.name}
              name="name"
              placeholder="Name"
              onChange={this.handleChange}
            />
          </div>
          <div>
            <input
              value={this.state.phone}
              name="phone"
              placeholder="Phone"
              onChange={this.handleChange}
            />
          </div>
          <button onClick={this.handleToggleEdit}>Apply</button>
          <button onClick={this.handleRemove}>Delete</button>
        </div>
      );
    }


    // 일반모드
    const {
      name, phone
    } = this.props.info;
    
    return (
      <div style={style}>
        <div><b>{name}</b></div>
        <div>{phone}</div>
        <button onClick={this.handleToggleEdit}>Edit</button>
        <button onClick={this.handleRemove}>Delete</button>
      </div>
    );
  }
}

export default PhoneInfo;
```
## 이번 주 감상
이렇게 해서 벨로퍼트님의 전화번호부 튜토리얼을 다 따라해보았는데, 이걸... 안보고 저 스스로 다 만들 수 있을지 한번 테스트해보려고 합니다.
