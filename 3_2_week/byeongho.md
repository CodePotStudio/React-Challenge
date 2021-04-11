# 랜딩 페이지에 비디오들 나타나게 하기

1. 빈 랜딩 페이지 생성
2. 비디오 카드 Template 만들기
3. 몽고 DB에서 모든 비디오 데이터 가져오기

useEffect → class Component에서 ComponentDidMount

- 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 실행 할 수 있도록 하는 HOOK이다.
- useEffect { function , deps }
1. function : 수행 하고 자 하는 작업
2. deps : 배열 형태이며, 배열 안에는 검사하고자 하는 특정 값 or 빈 배열 (dependency)

렌더링 될때 한번만 실행하고 싶을때는 deps 위치에 빈 배열을 넣는다.

```jsx
useEffect(() => {
	console.log('렌더링 될 때 마다 실행 된다');
}
```

특정 값이 업데이트 될 때 실행하고 싶을 때는 deps 위치의 배열 안에 검사하고 싶은 값을 넣어 준다

```jsx
useEffect(() => {
	console.log(name);

}, [name]);
```

뒷정리 함수 component가 unmount & update되기 직전

```jsx
useEffect(() => {
	console.log(name);
	return() => {
	};
}, []);
```

1. Video Upload

```jsx
const [Video, setVideo] = useState([])

    //Dom이 Load되자마자 무엇을 할 것인지? input이 비어있으면 한번만 실행 한다.
    //class component에서는 compoenet Did mount
    useEffect(() => {
        
        Axios.get('/api/video/getVideos')
        .then(response => {
            if (response.data.success) {
                console.log(response.data)
                setVideo(response.data.videos)
            } else {
                alert('비디오 가져오기를 실패 했습니다.')
            }
        })
    }, [])
```

[2.](http://2.Video) Video Card 쪽

```jsx
const renderCards = Video.map((video,index) => {
        var minutes = Math.floor(video.duration / 60);
        var seconds = Math.floor(video.duration - minutes * 60);

				//화면 사이즈에 따라 비디오나오는 갯수 지정하기 feat antd
        return <Col lg={6} md={8} xs={24}>
            <div style={{ position: 'relative' }}>
                <a href={`/video/${video._id}`} >
                <img style={{ width: '100%' }} alt="thumbnail" src={`http://localhost:5000/${video.thumbnail}`} />
                <div className=" duration"
                    style={{ bottom: 0, right:0, position: 'absolute', margin: '4px', 
                    color: '#fff', backgroundColor: 'rgba(17, 17, 17, 0.8)', opacity: 0.8, 
                    padding: '2px 4px', borderRadius:'2px', letterSpacing:'0.5px', fontSize:'12px',
                    fontWeight:'500', lineHeight:'12px' }}>
                    <span>{minutes} : {seconds}</span>
                </div>
                </a>
            </div><br />
            <Meta
                avatar={
                    <Avatar src={video.writer.image} />
                }
                title={video.title}
            />
            <span>{video.writer.name} </span><br />
            <span style={{ marginLeft: '3rem' }}> {video.views}</span>
            - <span> {moment(video.createdAt).format("MMM Do YY")} </span>
        </Col>
    })
```

3. Video Routes

```jsx
router.get("/getVideos", (req, res) => {

    //비디오를 DB에서 가져와서 클라이언트에 보낸다.
        //비디오 콜렉션안에 모든 비디오를 가져 온다.
        Video.find()
        .populate('writer')
        .exec((err, videos)=> {
            if(err) return res.status(400).send(err);
            res.status(200).json({ 
                success:true , videos})
        })
    })
```

### 비디오 디테일 페이지 만들기

1. 비어 있는 비디오 디테일 페이지 생성
2. 비디오 디테일 페이지를 위한 Route 만들기
3. 비디오 디테일 페이지 Template 만들기
4. MongoDB에서 비디오 데이터 가져오기
5. 가져온 데이터들을 스크린에 출력 한다.

```jsx
if (VideoDetail.writer) {
        return (
        
            <Row gutter={[16,16]}>
                <Col lg={18} xs={24}>
                <div style={{width: '100%', padding:'3rem 4rem'}}>
                <video style={{ width: '100%' }} src={`http://localhost:5000/${VideoDetail.filePath}`} controls></video>
                    <List.Item 
                        actions
                        >
                            <List.Item.Meta
                                avatar={<Avatar src={VideoDetail.writer.image} />}
                                title={VideoDetail.writer.name}
                                description={VideoDetail.description}
                            />
                        </List.Item>

                </div>

                </Col>
                <Col lg={16} xs={24}>
                    Side Videos
                </Col>

            </Row>
    )
    } else {
        return (
            <div>...loading</div>
        )
    }
```

- Video Detail 정보를 가져오기 전에 Rendering이 되어서, if 문으로 바꾸어 주었다. 하지만 옵셔널 체이닝을 이용해서 이런식으로 구현해도 된다.

```jsx
avatar={<Avatar src={VideoDetail.writer?.image} />}
         title={VideoDetail.writer?.name}
         description={VideoDetail.description}
```

- Route추가
- Route에 Id를 가져오는 이유는 props에 videoId를 가져와서, Api post시 사용하기 위함.

```jsx
<Route exact path="/video/:videoId" component={Auth(VideoDetailPage, true)} />
```

```jsx
useEffect(() => {
        Axios.post('/api/video/getVideoDetail', variable)
        .then(response => {
            if(response.data.success) {
                console.log(response.data.VideoDetail.filePath)
                setVideoDetail(response.data.VideoDetail)
            } else {
                alert('비디오 정보를 가져오기 실패 했습니다.')
            }
        })
    }, [])
```

```jsx
router.post("/getVideoDetail", (req, res) => {
    Video.findOne({ "_id" : req.body.videoId })
    .populate('writer')
    .exec((err, VideoDetail) => {
        if(err) return res.status(400).send(err)
        return res.status(200).json({ success : true , VideoDetail })
    })
});
```

### Ant Design

- [https://ant.design/components/overview/](https://ant.design/components/overview/)
- 중국에서 만든 웹 , 앱 디자인 개발을 위한 프레임 워크
- 디자인에 소요되는 시간을 줄 일 수 있다.
- Component로 되어있어서, Hooks로 적용 시키는 데 조금 시간이 걸렸다.
- Rating 구현해 보기

- 예제 코드

```jsx
import { Rate } from 'antd';

const desc = ['terrible', 'bad', 'normal', 'good', 'wonderful'];

class Rater extends React.Component {
  state = {
    value: 3,
  };

  handleChange = value => {
    this.setState({ value });
  };

  render() {
    const { value } = this.state;
    return (
      <span>
        <Rate tooltips={desc} onChange={this.handleChange} value={value} />
        {value ? <span className="ant-rate-text">{desc[value - 1]}</span> : ''}
      </span>
    );
  }
}

ReactDOM.render(<Rater />, mountNode);
```

Hooks를 이용해서 useState로 바꿔준다

```jsx
const desc = ['terrible', 'bad', 'normal', 'good', 'wonderful'];
    const [Rating, setRate] = useState(3)

    const handleChange = (e) => {
        setRate(e.currentTarget.value)
    }
```

```jsx
<span>
      <Rate tooltips={desc} onChange={handleChange} value={setRate} />
      {setRate ? <span className="ant-rate-text">{desc[setRate - 1]}</span> : ''}
</span>
```

디자인이 많이 필요하지 않은 어드민 페이지 같은 곳에서 쓰면 좋겠다는 생각이 들었다. 여러가지 Layout이나 Components들을 사용해서 익숙해지면 화면 그리는 시간을 많이 줄 일 수 있을거 같다.
