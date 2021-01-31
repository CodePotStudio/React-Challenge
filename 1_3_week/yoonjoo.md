# 3주차
* 이번 주는 정말 너무 바빠서 주중에 리액트 공부를 전혀 못했는데, 어제오늘 시도해본 것을 정리해보겠습니다.
* 지난주까지는 노마드코더 사이트에서 강의 하나를 들었고, 영화리스트 보여주는 걸 따라해보았습니다. 그런데 그냥 따라만 하니까 막상 혼자서는 아무것도 못만드는 것 같아서 뭔가 시도해보기로 했습니다.

## 표를 만들어보려고 시도함
* 우선... 노마드코더 강의를 듣고 나서, 유튜브에서 나동빈님의 비디오를 몇 개 보았습니다. 거기서 material-ui라는 걸 다운받아서 쓰길래 저도 한번 해봤습니다.
```
npm install @material-ui/core

import Paper from '@material-ui/core/Paper';
import Table from '@material-ui/core/Table';
import TableBody from '@material-ui/core/TableBody';
```
그러고 나서 import를 해주는데, 이게... table 구성요소마다 다 임포트를 해줘야하는 게 좀 귀찮단 생각이 들었어요. 어쨌든, 그렇게 임포트를 하고 나서 또 저만의 스타일을 설정하고 싶으면 
```
onst styles = theme => ({
  root: {
    width: '500%',
    marginTop: theme.spacing.unit * 3,
    overflowX: "auto"
  },
  table: {
    minWidth: 1080
  }
})

class App extends React.Component {
 ....

}

export default withStyles(styles)(App);
```
저렇게 마지막에 export 에서 withStyle로 감싸서 export해야한다고 합니다. 
사실 표를 만들기로 한 이유는, 예전에 바닐라JS로 간단하게 2048 게임을 만들어본 적이 있는데 그걸 리액트로 시도해보고 싶었기 때문인데요. 표를 만드는 것에서부터 막힐 줄은 몰랐습니다ㅠㅠ 
일단... 각 셀을 그리는 걸로 할까, 아니면 각 row를 그리는 걸로 고민하다가, 일단 각 row를 그리는 걸로 코드를 써보았습니다.

```
import React from 'react';
import TableRow from '@material-ui/core/TableRow';
import TableCell from '@material-ui/core/TableCell';

class Row extends React.Component {
    render() {
       return (
            <TableRow>
                <TableCell>{this.props.cell0}</TableCell>
                <TableCell>{this.props.cell1}</TableCell>
                <TableCell>{this.props.cell2}</TableCell>
                <TableCell>{this.props.cell3}</TableCell>
            </TableRow>
       )
    }
}

export default Row;
```
그리고 App.js 안에 있는 App클래스에다가 쓴 것은,
```
class App extends React.Component {
  state = {
    board:
    {
      row0: {
        id: "r0",
        c0: 1,
        c1: 2,
        c2: 3,
        c3: 0
      },
      row1: {
        id: "r1",
        c0: 2,
        c1: 2,
        c2: 3,
        c3: 4
      },
      row2: {
        id: "r2",
        c0: 6,
        c1: 2,
        c2: 3,
        c3: 9
      },
      row3: {
        id: "r3",
        c0: 4,
        c1: 2,
        c2: 3,
        c3: 2
      }
    }
  };


  render() {
    const { classes } = this.props;
    return (
      <Paper className={classes.root}>
        <Table className={classes.table}>
          <TableBody>
            {this.state.board.map(c => {
                return(<Row key={c.id}
                  cell0={c.c0}
                  cell1={c.c1}
                  cell2={c.c2}
                  cell3={c.c3} />)
                  ;})}
          </TableBody>
        </Table>
      </Paper>
    );
  }
}
```
* 사실 제가 자바스크립트로 썼던 2048 에서 `var board = Array(Array(0, 0, 0, 0), Array(0, 0, 0, 0), Array(0, 0, 0, 0), Array(0, 0, 0, 0));`를 처음에 만들고, 
키보드로 버튼을 누를 때마다 어레이 전체를 업데이트하고 그걸 다시 그리는 식으로 만들었었습니다. 그런데 그렇게 하니까 매번 누를 때마다 페이지 전체가 업데이트되는 게 싫었고,
리액트라면 그 수정된 셀만 변하도록 할 수 있지 않을까, 생각해서 시작한 건데요. 그래서 여기서도 state에다가 board: [[0,1,2,3],[3,1,2,3,]...] 이런 식으로 쓰려다가, 왠지 저렇게 쓰는게
더 보기 좋은 것도 같아서 써봤습니다. 그런데 저렇게 써도 되는 건지 스스로 확신이 없네요... 
(학교에서 자바로만 과제를 하고, 자바스크립트를 접한지가 얼마 안되어서 자꾸 간단한 거 하나 쓰는 것도 헷갈리고 확신이 없습니다ㅠㅠ 기본기부터 다져야겠다는 생각을 또 하게 되네요.) 

* 어쨌든, 해보고 싶었던 것은... 저렇게 state를 초기설정 해놓고, render()에서 각 줄마다 '행'을 확인해서 Row 컴포넌트를 만드려고 했던 것입니다. 그래서 map을 이용해서 각 행(row0, row1...)을 확인하고, 
각 행마다 <Row>를 리턴해서 화면에 쫘악 테이블이 나오게 하는 게 계획이었죠. 그런데 자꾸 에러가 뜨네요. `TypeError: this.state.board.map is not a function`라고 뜨는데 왜 이게 안된다는 것인지 모르겠습니다.
전에 인터넷에서 보고 따라했던 예제들에서는 이렇게 map을 이용해서 배열안에 있는 각 요소를 확인하던데 말이지요.

## 각 셀의 내용을 어떻게 업데이트할까 생각함
* state를 업데이트하면 리액트가 똑똑하게 변경된 것만 화면에 업데이트해준다고 강의에서 그랬던 것 같아요. 그럼 만약 내가 board[0][1] (이 코드에서는 board.row0.c1)를 9로 바꾼다거나 해서 그것만 변하면... 그부분만 바뀌는걸까, 아니면 
표 전체를 업데이트하게 되는 건지 궁금해졌습니다. 
* state를 변경할 때는 setState를 써서 하면 된다고 하는데, (지금 표조차 제대로 안띄워지는 상황이라 실험해볼 수는 없었지만) 표가 제대로 보이는 상황이라면 일단 이런 걸 테스트해보고 싶었어요. 
아직 키보드 액션을 어떻게 하는지 모르는 상태니까, 버튼을 눌렀을 때 배열 내용이 바뀌도록 한번 테스트해보고 싶었는데요. 
우선 테스트 위에 `<button onClick={this.change}>Change</button>`라는 버튼 하나가 뜨도록 만들어놓고,
```
change = () => {   //버튼 onClick할 때 실행될 함수
   this.setState({board.row0.c1: 9});
};
```
이렇게 해서 한번 테스트해보고 싶었는데, 저 setState에 쓸 말이 확실치가 않습니다. 저렇게 하면 row0.c1에 있는 저 부분이 바뀔지 궁금하네요. 

## 궁금한 것 요약
* 어쨌든 저 표가 화면에 나오도록 고치고 싶습니다. 그래야 저걸 봐가면서 이것저것 시도하면서 연습이 될텐데요... 
* 저렇게 row단위로 그리는 게 좋을지, 아니면 cell단위로 컴포넌트를 만드는게 좋을지 (아니면 별로 상관이 없는지) 잘 모르겠습니다. 목표는 2048을 만드는 거라서, 키보드로 버튼 누를 때마다 각 셀의 내용이 바뀔텐데... 
