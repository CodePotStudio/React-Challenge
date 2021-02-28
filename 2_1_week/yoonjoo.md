# 1주차 리뷰

새로 참여하시는 분들 반갑고, 계속 챌린지 하시는 분들 이번에도 잘 부탁드립니다 :) 
저는 외국에서 컴공과에 다니는 30대 늦깎이 대학생인데요, 학교에 웹개발 관련 강의가 딱 하나 있는데 내년되어야 들을 수 있어서 지금은 독학하고 있고, 리액트에 관심이 생겨서 챌린지에 참여하고 있습니다. 
이번 한달동안은 다른 분들 리뷰에 덧글도 적극적으로 달도록 노력해보겠습니다.

## 이번 챌린지 목표: 가계부 홈페이지 만들기
사실 예전에 JSP로 만든 게 있어서 그걸로 파트너와 생활비를 정리하고 정산하고 있는데, 매달 내역을 보려고 버튼을 클릭할 때마다 모든 페이지가 새로고침되는 게 마음에 안들어서 리액트에 관심을 갖게 되었습니다. 
지난 챌린지를 하면서 노마드코딩의 강의를 들었고, 나동빈님의 유튜브 강의를 하나 들었습니다. 이제는 조금씩 시도해보면서 만들어가야할 것 같아서 일단 나동빈님의 강의 때 따라한 코드를 뼈대로 시작해보기로 했습니다.

1. 우선, MySQL 데이터베이스를 세팅했습니다. 
2. 나동빈님 강의 들은 것 -특히 DB에 있는 내용을 가져오고, form에서 작성한 걸 DB로 보내는 부분을 복습했습니다.
3. 노마드코딩 강의에서 라우팅 이용하는 부분을 다시 복습했습니다.

## 일단 생각하는 뼈대
+ components
  + Log.js
  + AddLog.js
  + UpdateLog.js
  + DeleteLog.js
+ routes
  + Home.js
  + Cashbook.js
  + Calculator.js
  + Statistics.js

유저에 따라서 권한을 다르게 주고 싶은데 그걸 어떻게 해야할지 아직 잘 모르겠네요. 저랑 제 파트너, 그리고 이 페이지를 시험해보고 싶은 게스트 계정 이렇게 하고 싶은데요.(아무래도 포폴로 쓰고 싶은 마음이 있으므로..)
아무래도 유저 관련된 데이터베이스를 만들고, 로그인하고 나서 User 컴포넌트를 만들도록 시켜야하지 않을까 생각중입니다.

## 일단 손을 움직이자
### server.js
```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = process.env.PORT || 5000;
const fs = require('fs');
 
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

const data = fs.readFileSync('./database.json');
const conf = JSON.parse(data);
const mysql = require('mysql');

const connection = mysql.createConnection({
  host: conf.host, 
  user: conf.user,
  password: conf.password,
  port: conf.port,
  database:conf.database
})

connection.connect();


app.get('/api/cashbooks',(req,res) => {
    connection.query(
      "SELECT * FROM CASHBOOK",
      (err, rows, fields) => {
        res.send(rows);
      }
    )
});

app.post('/api/cashbooks', (req,res) => {
  let sql = 'INSERT INTO CASHBOOK (userID, date, amount, category, place) VALUES (?, ?, ?, ?, ?)';
  let userID = req.body.userID;
  let date = req.body.date;
  let amount = req.body.amount;
  let category = req.body.category;
  let place = req.body.place;
  let params = [userID, datum, belopp, category, place];
  connection.query(sql, params, (err, rows, fields) => {res.send(rows);});
})

app.delete('/api/cashbooks/:id', (req, res) => {
  let sql = 'DELETE FROM CASHBOOK WHERE id=?';
  let params = [req.params.id];
  connection.query(sql, params, (err, rows, fields) => {res.send(rows);});
})

app.listen(port, () => console.log(`Listening on port ${port}`));
```
나동빈님 리액트 강의에 나오는 코드를 조금 수정해보았습니다. 다행히 세팅해놓은 데이터베이스에 잘 접속하는 것 같아요. 데이터베이스는 아마존 서비스를 이용했습니다. 
add랑 delete는 썼는데 아직 update하는 코드를 안써서 그건 다음 주에 하겠습니다. 뭐 그냥 비슷하게 app.update(....) 하고, 그 안에다가 데이터베이스 update하는 쿼리를 넣으면 되겠죠?

### Log.js
이 부분은, 가계부에서 예를 들면...
이름 / 날짜 / 지출금액 / 카테고리 / 장소 
이렇게 나오는 각 항목입니다. 그래서 log 컴포넌트를 나중에 Cashbook.js에서 표로 한줄씩 나타낼 생각입니다. 
```
import React from 'react';
import {TableRow, TableCell} from '@material-ui/core';
import DeleteLog from './DeleteLog';

class Log extends React.Component {
    render() {
       return (
            <TableRow>
                <TableCell>{this.props.userID}</TableCell>
                <TableCell>{this.props.date}</TableCell>
                <TableCell>{this.props.amount}</TableCell>
                <TableCell>{this.props.category}</TableCell>
                <TableCell>{this.props.place}</TableCell>
                <TableCell><DeleteLog stateRefresh={this.props.stateRefresh} id={this.props.id}/></TableCell>
            </TableRow>
       )
    }
}

export default Log;
```
물론 이것도 나동빈님 강의에서 배운 것을 수정한 것이지만... 여기서 저는 엄데이트 하는 항목을 하나 더 추가할 생각입니다.
DeleteLog는 그 부분을 클릭하면 로그의 id를 보내서, 데이터베이스에서 지우도록 하는 기능이 들어있습니다.

```
import React from 'react';

class DeleteLog extends React.Component {
    deleteLog(id) {
        const url = '/api/cashbooks/' + id;
        fetch(url, {
            method: 'DELETE'
        });
        this.props.stateRefresh();
    }

    render() {
        return (
            <button onClick={(e) => {this.deleteLog(this.props.id)}}> Delete</button>
        )
    }
}

export default DeleteLog;
```
이렇듯, 버튼을 만들고, 버튼을 누르면 deleteLog가 실행되도록 하고, deleteLog는 id와 함께 url에 연결해서 아까 server.js에 있던 delete를 이용해 DB에서 해당항목을 지우고,
그리고 stateRefresh를 이용해 state를 업데이트해서 렌더링되도록 합니다. stateRefresh는 Log컴포넌트가 만들어지는 곳에서부터 계속 전달전달되는 것인데요, 아래의 Cashbook.js에 있습니다.

### Cashbook.js
```
const styles = theme => ({
  root: {
    width: '100%',
    marginTop: theme.spacing.unit *3,
    overflowX: "auto"
  },
  table: {
    minWidth: 1080 
  },
  progress: {
    margin: theme.spacing.unit *2
  }

})

class Cashbook extends Component {

  constructor(props) {
    super(props);
    this.state = {
      logs: '',
      completed: 0
    }
  }

  stateRefresh = () => {
    this.setState({
      logs: '',
      completed: 0
    });
    this.callApi()
      .then(res => this.setState({logs: res}))
      .catch(err => console.log(err));
  }

  componentDidMount() {
    this.timer = setInterval(this.progress,20);
    this.callApi()
    .then(res => this.setState({logs: res}))
    .catch(err => console.log(err));
  }

  callApi = async () => {
    const response = await fetch('/api/cashbooks');
    const body = await response.json();
    return body;
  }

  progress = () => {
    const { completed } = this.state;
    this.setState({completed: completed >= 100 ?  0 : completed +1});
  }

  render() {
    const {classes} = this.props;
    return (
      <div>
      <Paper className = {classes.root}>
        <Table className = {classes.table}>
          <TableHead>
            <TableRow>
              <TableCell>User</TableCell>
              <TableCell>Date</TableCell>
              <TableCell>Amount</TableCell>
              <TableCell>Category</TableCell>
              <TableCell>Place</TableCell>
              <TableCell>setting</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {this.state.logs ? this.state.logs.map(c => {
              return (<Log stateRefresh={this.stateRefresh} key={c.id} id={c.id} userID={c.userID} date={c.date}
                amount={c.amount} category={c.category} place={c.place} />);
            }) : 
              <TableRow>
                <TableCell colSpan = "6" align="center">
                <CircularProgress className={classes.progress} variant="determinate" value={this.state.completed} />
                </TableCell>
              </TableRow>
            }
          </TableBody>
        </Table>
      </Paper>
      <AddLog stateRefresh = {this.stateRefresh}/>
      </div>
    );
  }
}

export default withStyles(styles)(Cashbook);

```
여기가 이제... 각 로그들을 표로 만들어서 화면에 띄우는 부분인데요, 바로 여기서부터 stateFresh()가 Log컴포넌트 만들 때 들어가고, Log컴포넌트에서 DeleteLog컴포넌트를 만들때 그게 또 전달되게 됩니다. 
저는 사실 이 부분이 제일 마음에 들었어요. 컴포넌트를 만들면서 이렇게 값 뿐만 아니라 함수도 전달, 전달 할 수 있는 게 앞으로도 많이 유용하겠다는 생각이 들었습니다.

### AddLog.js
```
import React from 'react';
import {post} from 'axios';

class AddLog extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            userID: '',
            date: '',
            amount: '',
            category: '',
            place: ''
        }
    }

    handleFormSubmit = (e) => {
        e.preventDefault();
        this.addLog()
        .then((response) => {
            console.log(response.data);
            this.props.stateRefresh();
        })
        this.setState({ // initialize after submit
            userID: '',
            date: '',
            amount: '',
            category: '',
            place: ''
        })
    }

    handleValueChange = (e)  => {
        let nextState = {};
        nextState[e.target.name] = e.target.value;
        this.setState(nextState);
    }

    addLog = () => {
        const url = '/api/cashbooks';
        const formData = new FormData();
        formData.append('userID', this.state.userID);
        formData.append('date', this.state.date);
        formData.append('amount', this.state.amount);
        formData.append('category', this.state.category);
        formData.append('place', this.state.place);
        const config = {
            headers: {
                'content-type' : 'multipart/form-data'
            }
        }
        return post(url, formData, config); //using Axios
    }

    render() {
        return (
            <form onSubmit = {this.handleFormSubmit}>
                <h1>Add customer</h1>                
                User: <input type="text" name="userID" value={this.state.userID} onChange={this.handleValueChange} /><br />
                Date: <input type="text" name="date" value={this.state.date} onChange={this.handleValueChange} /><br />
                Amount: <input type="text" name="amount" value={this.state.amount} onChange={this.handleValueChange} /><br />
                Category: <input type="text" name="category" value={this.state.category} onChange={this.handleValueChange} /><br />
                Place: <input type="text" name="place" value={this.state.place} onChange={this.handleValueChange} /><br />
                
                <button type="submit">Submit</button>
            </form>
        )
    }
}

export default AddLog;
```
일단 처음에 state를 비워놓고, 폼에 무엇을 입력하는지에 따라서 state를 업데이트한 후, submit을 하면 아까 server.js에 있는 app.post로 이동해서 데이터베이스에 입력을 하게 됩니다. 

### 잘 동작하나? 
그게 문제입니다. 사실 이걸 테스트해보기 전에 이미 지난 챌린지 때 열심히 따라했던 코드들을 다시 한번 `npm start` 했는데 새 창이 안열리는 거예요. 
localhost:5000 수동으로 주소창에 입력도 했는데 `cannot get /` 이거만 뜨네요. 지금 새로 쓴 코드들도 마찬가지입니다. 'Listening on port 5000'이라고 뜨는 걸 보니 서버에 잘 연결한 것 같은데
페이지에 뭐 뜨는 게 없네요.... 왜일지 궁금합니다. 아, App.js에는 이렇게 썼어요.

### App.js
```
import React from "react";
import { HashRouter, Route } from "react-router-dom";
import Cashbook from "./routes/Cashbook";
import "./App.css";

function App() {
  return (
    <HashRouter>
      <Navigation />
      <Route path="/" exact={true} component={Cashbook} />
    </HashRouter>
  );
}

export default App;
```
중간에 페이지 이름도 좀 바꾸고 변수들 이름도 바꿔서, 제가 꼼꼼하게 못바꾼 부분들이 있어서 그런가... 이건 내일 조사를 좀 해봐야겠습니다. 이렇게 리뷰를 마칩니다. 행복한 한 주 보내세요!
