## 이번 챌린지 목표
- 한달 쉬었더니 정말 그전에 공부한 걸 다 잊어버려서, 처음부터 차근차근 다시 공부해볼 예정입니다. 
- Velopert님의 블로그 https://velopert.com/3634 를 보고 공부하고 있습니다. 

## 이번 주에 배운 것
### Computed Property Name

```
import React, {Component} from 'react';

class PhoneForm extends Component {
    state={
        name: '',
        phone: ''
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
        })
    }

    render() {
        return(
            <form>
                <input
                    placeholder = "name"
                    value={this.state.name}
                    onChange={this.handleChange}
                    name="name"
                />
                <input
                    placeholder = "phone number"
                    value={this.state.phone}
                    onChange={this.handleChange}
                    name="phone"
                />
                <div>{this.state.name} {this.state.phone}</div>
            </form>
        )
    }
}

export default PhoneForm;
```

handleChange 안에 있는 대괄호를 처음 봐서 저게 무엇인지 궁금했는데, ES6의 문법 중 하나인 Computed Property Name라는 것을 알게 되었습니다. 
입력폼이 '이름'과 '전화번호' 각각 두 개가 있고, 거기에 뭔가 입력하면 그게 화면에 표시되도록 하는 것인데, 각각 handleChange를 따로 만들 필요없이, 
폼이 가진 name에 따라서 state안에 있는 변수를 바꿔주는 것 같습니다. 굉장히 유용하게 쓸 수 있을 것 같네요. 이에 대한 내용은 https://yorr.tistory.com/13 를 참고했습니다. 

### 부모컴포넌트에게 정보 전달하기
```
class PhoneForm extends Component {
  state = {
    name: '',
    phone: ''
  }
  handleChange = (e) => {
    this.setState({
      [e.target.name]: e.target.value
    })
  }
  handleSubmit = (e) => {
    // 페이지 리로딩 방지
    e.preventDefault();
    // 상태값을 onCreate 를 통하여 부모에게 전달
    this.props.onCreate(this.state);
    // 상태 초기화
    this.setState({
      name: '',
      phone: ''
    })
  }
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
      ....
      );
  }
}
```
폼을 submit할때 handleSubmit이 실행되는데, 먼저 e.preventDefault()가 실행됩니다. 원래는 폼에서 submit을 하면 페이지를 다시 불러오게 되지만,
그걸 방지하기 위해(그래서 state가 리셋되는 걸 방지하기 위해) 저 preventDefault()를 이용한다고 합니다.

```
class App extends Component {
  handleCreate = (data) => {
    console.log(data);
  }
  render() {
    return (
      <div>
        <PhoneForm
          onCreate={this.handleCreate}
        />
      </div>
    );
  }
}
```
PhoneForm 컴포넌트를 만들었을 때 handleCreate(받은 데이터를 출력하는 메소드)를 onCreate함수라는 이름으로 전달해주었는데요, 
그래서 위에 handleSubmit 안에서 props로 받은 onCreate함수를 사용할 수 있었던 것입니다. 
예전에도 한번 본적이 있었지만, 이렇게 컴포넌트를 만들 때 함수를 전달할 수 있다는 점이 흥미로웠습니다.

### 배열 다루기
처음에 배열에 익숙하지 않아서 이 스터디에서 도움을 받았던 게 기억납니다! >_< 
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

  render() {
     return (
      <div>
        <PhoneForm
          onCreate={this.handleCreate} />
          <PhoneInfoList data = {this.state.information} />
      </div>
    )
  }
}
```
state안에 information 이라는 배열을 만들었고, 그 안에 각각 {id, name, phone} 데이터를 넣었습니다. 그리고 데이터를 추가할 때 setState를 통해서 배열을 업데이트해야하는데, concat을 이용할 수가 있네요. 저렇게 해서 id는 하나씩 증가하고, 데이터를 넣을 수 있도록 했습니다. 
저 ...data에서 점 세개 있는게 궁금해서 찾아보았는데, 자바스크립트의 spread syntax라는 걸 알게 되었습니다. Spread syntax는 배열안에 있는 것을 각각 개별요소로 사용할 수 있도록 하는 모양입니다. 즉, data안에 있는 요소를 따로따로 떼어서 id와 함께 데이터를 {id, name, phone} 형태로 만들고, information에 추가하기 위해 쓰였습니다. 

```
class PhoneInfoList extends Component {
  static defaultProps = {
    data: []
  }

  render() {
    const { data } = this.props;
    const list = data.map(
      info => (<PhoneInfo key={info.id} info={info}/>)
    );

    return (
      <div>
        {list}    
      </div>
    );
  }
}
```
그리고 리스트를 예쁘게 보여주기 위해 PhoneInfoList 컴포넌트를 만드는데 여기서 map을 써서, 배열 안에 있던 각 데이터 {id, name, phone}를 이용해 PhoneInfo 컴포넌트를 각각 만들 수 있었습니다. 이렇듯, 배열을 직접 수정하기보다는 기존의 배열로 새 배열을 만드는 함수들(map, concat, filter, slice)같은 걸 사용해야한다는 것을 알게 되었습니다. 
```
class PhoneInfo extends Component {
  static defaultProps = {
    info: {
      name: 'Name',
      phone: '010-0000-0000',
      id: 0
    }
  }
  
  render() {
    const style = {
      border: '1px solid black',
      padding: '8px',
      margin: '8px'
    };

    const {
      name, phone, id
    } = this.props.info;
    
    return (
      <div style={style}>
        <div><b>{name}</b></div>
        <div>{phone}</div>
      </div>
    );
  }
}
```
그리고 만약 어떤 이유에서 info 값이 전달이 안되면 동작이 되지 않을테니까, info값이 없을 때에도 크래쉬 되지 않도록 defaultProps를 설정해주는 것이었고요. 

## 한주 총평 & 다음 주 계획
아직도 저는 상태 업데이트, 특히 배열을 업데이트하는 게 낯이 설어요. 이 부분을 좀더 연습해보려고 하고 filter같은 것도 연습해보려고 합니다. 
전화번호부 형태를 잡는 것까지 했는데, 그 다음 강좌는 수정하고 삭제하는 기능을 만드는 것이네요. 그걸 하면서 더 많이 배우게 될 것 같습니다. 
