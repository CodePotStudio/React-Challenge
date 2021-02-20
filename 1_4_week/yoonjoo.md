## 이번 주에 한 것들
* 리액트를 배워서 해보고 싶었던 게 크게 두 가지인데요, 하나는... 지난 주에 낑낑댔던 2048이었고, 다른 하나는 간단한 가계부 홈페이지를 만드는 거였습니다. 
나동빈님 강좌 중에 고객관리프로그램 만드는 게 있는데, 이걸 잘 이용하면 제가 원하는 홈페이지를 만들 수 있을 것 같아서 보기 시작했어요. 

### ArrowKey 이용하기
* 2048을 어떻게 만들까 생각해보다가, 궁금했던 기능을 하나하나 배워가기로 다짐하고... 화살표 키보드를 어떻게 읽는지 알아보았습니다. 
* 찾아보니 arrow-keys-react를 사용하면 되는 것 같았습니다. 
``` 
ArrowKeysReact.config({
      up: () => {
        let board = this.state.board;
        board[1].c1 = board[1].c1 + 1;
        this.setState({ board });
      },
      down: () => {
        let board = this.state.board;
        board[1].c1 = board[1].c1 - 1;
        this.setState({ board });
      }
    });
```
이런 걸 하나 추가해준 다음, render에다가는
```
<div {...ArrowKeysReact.events} tabIndex="1">
``` 
이것을 추가해주었습니다. 
일단... 시험삼아, 위 화살표를 누르면 표에서 board[1][1]에 해당하는 숫자가 하나씩 증가하고, 아래 화살표를 누르면 숫자가 줄어들도록 테스트해본 것인데, 정말로 되어서 좀 감격했습니다. 
사실 그 전까지 제가 setState를 제대로 이해하지 못해서, this.setState({board[1].c1 = this.state.board[1].c1 +1}) 이런 식으로 쓰는 등 뻘짓을 했는데,
저렇게 board를 저장하고, 수정한 후, 수정된 board를 다시 setState를 통해 저장하면 된다는 것을 이번에 확실히 알게 되었습니다. 확실히 뻘짓을 많이 하고 배워야 기억에 남네요... 

### form 만들고 post 보내기
* 잠시 2048을 접고... 나동빈님 강의를 보면서 제가 나중에 가계부 홈페이지를 만들때 유용하겠다 싶은 걸 메모해두었습니다. 
* Amazon aws에 가입하고 mySQL 데이터베이스를 사용해서 따라해보았습니다. 
* form 양식에서 작성한 내용을 보내서 데이터베이스에 저장하고, 화면을 리프레시 하는 그 과정이 흥미로워서 메모해보았습니다. 
``` 
import React from 'react';
import {post} from 'axios'; 

class CustomerAdd extends React.Component {
    constructor(props) {
        super(props);
        this.state = { //form에서 받은 내용을 여기다 채워서 post로 보내는 모양임
            file: null,
            userName: '',
            birtday: '',
            gender: '',
            job: '',
            fileName: ''
        }
    }

  handleFormSubmit = (e) => { //제출 누르면 실행될 내용
        e.preventDefault();
        this.addCustomer() // 내용을 post 보내는 addCustomer를 실행해라
        .then((response) => {
            console.log(response.data);
            this.props.stateRefresh(); // DB입력 다 했다고 연락이 오거든 stateRefresh를 하거라
        })
        this.setState({ // 여기 있는 state를 초기화
            file: null,
            userName: '',
            birtday: '',
            gender: '',
            job: '',
            fileName: ''
        })
    }


    handleFileChange = (e) => {
        this.setState({
            file: e.target.files[0], //파일 여러개 선택해도 맨 첫번쨰 하나만 올라가도록 
            fileName: e.target.value 
        })
    }

    handleValueChange = (e)  => {
        let nextState = {};
        nextState[e.target.name] = e.target.value; 
    //textfield에 있는 값(e.target.value)를 nextState안에 e.target.name: e.target.value 이런 식으로 저장하나봄
        this.setState(nextState);
    }

    addCustomer = () => {
        const url = '/api/customers'; 
        const formData = new FormData();
        formData.append('image', this.state.file);
        formData.append('name', this.state.userName);
        formData.append('birthday', this.state.birthday);
        formData.append('gender', this.state.gender);
        formData.append('job', this.state.job);
        const config = { //이건 file이 있기 때문에 추가해야할 내용이라고 함
            headers: {
                'content-type' : 'multipart/form-data'
            }
        }
        return post(url, formData, config); 
    }

    render() {
        return (
            <form onSubmit = {this.handleFormSubmit}>
                <h1>Add customer</h1>                
                Profile image: <input type="file" name="file" file={this.state.file} value={this.state.fileName} onChange={this.handleFileChange} /><br />
                Name: <input type="text" name="userName" value={this.state.userName} onChange={this.handleValueChange} /><br />
                Birthday: <input type="text" name="birthday" value={this.state.birthday} onChange={this.handleValueChange} /><br />
                Gender: <input type="text" name="gender" value={this.state.gender} onChange={this.handleValueChange} /><br />
                Job: <input type="text" name="job" value={this.state.job} onChange={this.handleValueChange} /><br />
                <button type="submit">Submit</button>
            </form>
        )
    }
}

export default CustomerAdd;
``` 
그래서 저 addCustomer가 실행되면 formData에다가 현재 state에 있는 내용을 차곡차곡 담아서 보내고, 그걸 server.js에서 DB에 저장합니다.
재미있다고 생각한 것은 저 stateRefresh인데요, DB에 저장하고 나서 response가 오면 실행되는 것인데.. App.js에서부터 전달받은 props입니다.

### Props 전달, 부모 컴포넌트, 자식 컴포넌트
리액트에 관한 글들을 보면서 참 많이 본 단어들인데 사실 그렇게 와닿지 않았는데, 저 stateRefresh가 어떻게 호출되는지에 대해 생각하고 확인하다보니 이제 알게 된 것 같습니다. 
``` 
class App extends Component {

  constructor(props) {
    super(props);
    this.state = {
      customers: '',
      completed: 0
    }
  }

  stateRefresh = () => {
    this.setState({
      customers: '',
      completed: 0
    });
    this.callApi()
      .then(res => this.setState({customers: res}))
      .catch(err => console.log(err));
  }

...생략 ... 

  render() {
    const {classes} = this.props;
    return (
      <div>
     ...생략...
            {this.state.customers ? this.state.customers.map(c => {
              return (<Customer stateRefresh={this.stateRefresh} key={c.id} id={c.id} image={c.image} name={c.name}
                birthday={c.birthday} gender={c.gender} job={c.job} />);
            }) : 

      ...생략...
      <CustomerAdd stateRefresh = {this.stateRefresh}/>
      </div>
    );
  }
}
``` 
이렇게 App.js 에 stateRefresh()가 있고, state를 초기화한 후 다시 자료 받아다가 state를 채우는 역할인데, 이걸 컴포넌트를 만들 때 전달할 수 있다는 것을 알게 되었습니다.
아니, 정확히는... 컴포넌트를 만들 때 state에 있는 내용을 전달할 수 있다는 건 알고있었지만, 그게 함수가 될 수도 있다는 걸 이제 알았어요.
그래서 저렇게 Customer 컴포넌트를 만들때 stateRefresh를 전달하고, 또... Customer 컴포넌트 안에서 CustomerDelete 컴포넌트를 만들게 되는데 그때 또 이걸 전달해요.
그래서 나중에 CustomerDelete(고객목록에서 고객 하나를 지우는 거)에서, 고객 하나를 지운 후 화면을 다시 리프레시 하는 게 가능하도록 하는 것이고,
이런 기능 덕분에 홈페이지 화면 전체를 새로고침하는 게 아니라 state가 변할 때 렌더링을 다시 하는 게 가능한 것 같습니다. 

# 4주간의 스터디 후 소감
* 저번에 표 만드는 것 관련해서 궁금증을 남겼는데, 여러 분들께서 잘 알려주셔서 해결하고 또 배웠습니다. 정말 감사합니다! 
* 제가 정말 왕기초인데다가 개인적으로 너무 정신이 없었어서, 코멘트도 못 남기고 도움을 받기만 해서 죄송합니다. 
스터디모임이 계속 이어진다면 다음에는... 올려주신 내용 읽고 감상이라도 적도록, 그리고 실력을 끌어올려서 좀더 유익한 코멘트를 적도록 노력하겠습니다.
* 어쨌든, 다시 한 번 감사드리고, 스터디 이끌어주신 한울님 특히 감사드립니다! 행복한 설 명절 보내세요 :) 
