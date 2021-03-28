## 이번 챌린지 목표
- 한달 쉬었더니 정말 그전에 공부한 걸 다 잊어버려서, 처음부터 차근차근 다시 공부해볼 예정입니다. 
- Velopert님의 블로그 https://velopert.com/3634 를 보고 공부하고 있습니다. 

## 새로 접한 개념
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


