# 7주차

### Side Video Page 만들기

1. DB에서 비디오 목록 가져오기

Video Detail Page에서 모든 비디오 목록을 가져온 것처럼 가져 오면 된다.

```jsx
useEffect(() => {
        
        Axios.get('/api/video/getVideos')
        .then(response => {
            if (response.data.success) {
                console.log(response.data)
                setsideVideos(response.data.videos)
            } else {
                alert('비디오 가져오기를 실패 했습니다.')
            }
        })
    }, [])
```

2.Side Vedio UI그리기

```jsx
const renderSideVideo = sideVideos.map((video,index) => {

        var minutes = Math.floor(video.duration / 60);
        var seconds = Math.floor(video.duration - minutes * 60);

        return <div key = {index} style={{ display:'flex' , marginBottom:"1rem" ,padding : '0 2rem'}}>
            <div style={{ width : '40%' , marginRight: '1rem'}}>
                <a href>
                    <img style={{ width : '100%'}} src={`http://localhost:5000/${video.thumbnail}`} alt="thumbnail" />
                </a>
            </div>       
            <div style={{ width : '50%' , height : '100%'}}>
                <a href style = {{color : "gray"}}>
                    <span style={{ fontSize : '1rem' , color : 'black'}}>{video.title}</span><br />
                    <span> {video.writer.name} </span><br />
                    <span> {video.views} views </span><br />
                    <span> {minutes} : {seconds} </span><br />
                </a>
            </div>
        </div>
        
    })
```

- key 값으로 index값을 넘겨 줘야 한다.

### 구독 기능 만들기

1. Subscriber Model 만들기

→ UserTo , UserFrom 고려

→ props로 Subscribe button으로 userTo값을 가져 온다.

```jsx
actions={[<Subscribe userTo={VideoDetail.writer?._id}/>]}
                        >
```

```jsx
function Subscribe(props) {

    const [SubscribeNumber, setSubscribeNumber] = useState(0)
    const [subscribed, setSubscribed] = useState(false)

    useEffect(() => {
        let variable = { userTo: props.userTo  }
        Axios.post('/api/subscribe/subscribeNumber', variable)
        .then(response => {
            if (response.data.success) {
                setSubscribeNumber(response.data.SubscribeNumber)
            } else {
                alert('구독자 수 정보를 받아오지 못했습니다.')
            }
        })
        let subscribedVariable = { userTo: props.userTo, userFrom: localStorage.getItem('userId') }

        Axios.post('/api/subscribe/subscribed', subscribedVariable)
        .then(response => {
            if(response.data.success) {
                setSubscribed(response.data.subscribed)
            } else {
                alert('정보를 받아오지 못했습니다.')
            }
        })

    }, [])
```

 2. Subscribe Button UI 만들기

```jsx
<div>
            <button style= {{ backgroundColor: `${Subscribe ?  '#CC0000' : '#AAAAAAA'}`, borderRadius : '4px',
                              color : 'white' , padding : '10px 16px',
                              fontWeight : '500', fontSize: '1rem', textTransform: 'uppercase'
                              
                              }}
                              onClick
                              >
                   {SubscribeNumber} {Subscribe ? 'Subscribed' : 'Subscribe'}
            </button>
        </div>
```

- 구독 중 일때의 색상 고려해서 변경해준다.
- 구독 중 일때의 문구를 변경

3. 유저의 총 구독자 수

```jsx
useEffect(() => {
        let variable = { userTo: props.userTo  }
        Axios.post('/api/subscribe/subscribeNumber', variable)
            .then(response => {
                if (response.data.success) {
                    setSubscribeNumber(response.data.subscribeNumber)
                } else {
                    alert('Failed to get subscriber Number')
                }
        })
```

 4. 나의 구독 여부

- local storage에서 나의 userId값을 가져온다.

```jsx
let subscribedVariable = { userTo: props.userTo, userFrom: localStorage.getItem('userId') }

        Axios.post('/api/subscribe/subscribed', subscribedVariable)
        .then(response => {
            if(response.data.success) {
                setSubscribed(response.data.subscribed)
            } else {
                alert('정보를 받아오지 못했습니다.')
            }
        })
```

 5. 가져온 정보를 화면에 출력

### 구독하기 기능 만들기

1. 구독하기 기능 만들기  → Subscribe
2. 구독 취소 기능 만들기 → Unsubsribe
- Subscribe Button을 누를때 local storage에서 props를 통해서 userId를 가져온다.

```jsx
actions={[<Subscribe userTo={VideoDetail.writer?._id} userFrom={localStorage.getItem('userId')}/> ]}
```

```jsx
const onSuscribe = () => {
        let subscribeVariable = {
            
            userTo: props.userTo,
            userFrom: props.userFrom
        }

        //이미 구독 중이라면
        if(Subscribed) {
            Axios.post('/api/subscribe/unSubscribe', subscribeVariable )
            .then(response => {
                if(response.data.success) {
                    setSubscribeNumber(SubscribeNumber - 1)
                    setSubscribed(!Subscribed)
                } else {
                    alert('구독 취소 하는 데 실패 했습니다.')
                }
            })
        //아직 구독 중이 아니라면
        } else {
            Axios.post('/api/subscribe/Subscribe', subscribeVariable)
            .then(response => {
                if(response.data.success) {
                    setSubscribeNumber(SubscribeNumber + 1)
                    setSubscribed(!Subscribed)
                } else {
                    alert('구독 하는 데 실패 했습니다.')
                }
            })
        }

    }
```

### 구독 페이지 만들기

1. Subscription 페이지 만들기

→ landingPage와 거의 동일

 2. Subscription Page를 위한 Route만들기

→ app.js에 추가해 준다.

 3. Template 만들기

  4. 내가 구독한 유저의 비디오들 만 서버에서 가져오기

```jsx
router.post("/getSubscriptionVideos", (req, res) => {

    //Need to find all of the Users that I am subscribing to From Subscriber Collection 
    
    Subscriber.find({ 'userFrom': req.body.userFrom })
    .exec((err, subscribers)=> {
        if(err) return res.status(400).send(err);

        let subscribedUser = [];

        subscribers.map((subscriber, i)=> {
            subscribedUser.push(subscriber.userTo)
        })

        //Need to Fetch all of the Videos that belong to the Users that I found in previous step. 
        Video.find({ writer: { $in: subscribedUser }})
            .populate('writer')
            .exec((err, videos) => {
                if(err) return res.status(400).send(err);
                res.status(200).json({ success: true, videos })
            })
    })
});
```

- mongoDB : in method를 사용해서 내가 구독하고 있는 모든 사용자의 Id를 가져온다.

  5. 가져온 비디오 데이터들을 화면에 출력하기

### 리액트 프로젝트에서 타입스크립트 사용하기

→ 간단하게 알아보기 위해, velopert님의 글을 읽고 정리하였습니다.

1. JavaScript의 불편함
- 자동 완성 별로다.
- 함수 파라미터 타입을 체킹해주지 않는다.
- 리덕스를 쓸때 불편한다.
- 리액트 컴토넌트를 쓸 때 어떤 props를 넣어야 하는지 에디터에서 알 방법이 없다.

2. 써야 하는 이유

→ 처음엔 불편한지 모르지만 개발 생산성과 협업 그리고 IDE를 적극 활용 할 수 있게 해준다.

### 타입 스크립트 기초 연습

- 설치

```jsx
yarn global add typescript

tsc --init //설치 확인
```

> target: 컴파일된 코드가 어떤 환경에서 실행될 지 정의합니다. 예를들어서 화살표 함수를 사용하고 target 을 es5 로 한다면 일반 function 키워드를 사용하는 함수로 변환을 해줍니다. 하지만 이를 es6 로 설정한다면 화살표 함수를 그대로 유지해줍니다.
module: 컴파일된 코드가 어던 모듈 시스템을 사용할지 정의합니다. 예를 들어서 이 값을 common 으로 하면 export default Sample 을 하게 됐을 때 컴파일 된 코드에서는 exports.default = helloWorld 로 변환해주지만 이 값을 es2015 로 하면 export default Sample 을 그대로 유지하게 됩니다.
strict: 모든 타입 체킹 옵션을 활성화한다는 것을 의미합니다.
esModuleInterop: commonjs 모듈 형태로 이루어진 파일을 es2015 모듈 형태로 불러올 수 있게 해줍니다. (참고)

### 리액트 프로젝트에서 타입스크립트 사용하기

- 설치

```jsx
npx create-react-app ts-tutorial --template typescript
```

[https://velog.io/@velopert/using-react-with-typescript](https://velog.io/@velopert/using-react-with-typescript)

[https://velog.io/@velopert/typescript-basics](https://velog.io/@velopert/typescript-basics)
