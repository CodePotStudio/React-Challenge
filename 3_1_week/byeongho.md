# 5주차

### 비디오 업로드 하기

- RDBMS : Database → Tables → Rows → Columns
- MongoDB : Database → Collections → Documents → Fields

1. 비디오 Collection을 만들다. 

→ Write, Title, Description , Privacy , FilePath , Category, Views, Duration, Thumbnail

 2.  onSubmit Function을 만든다.

 3. 요청 할 데이터들을 서버로 보낸다.

 4. 보낸 데이터들을 MongoDB에 저장 한다.

1. Video Collection 만들기

```swift
const mongoose = require('mongoose');
const Scema = mongoose.Schema;

const videoSchema = mongoose.Schema( {

    //Id만 넣어도 User의 모든 정보를 가져 올 수 있다.
    writer: {
        type: mongoose.Schema.Types.ObjectId,
        ref:'User'
    },
    title : {
        type: String,
        maxlength: 50
    },
    description : {
        type: String
    },
    privacy: {
        type: Number
    },
    filePath: {
        type: String
    },
    category: {
        type: String,
    },
    //Views는 0부터 시작 하니까
    views: {
        type: Number,
        default: 0
    },
    duration: {
        type: String
    },
    thumbnail: {
        type: String
    }

//time stamp -> data와 update가 표시가 되게 된다.
} { timestamps : true })

const Video = mongoose.model('Video', videoSchema);

module.exports = { Video }
```

2.onSubmit function 만들기 & 요청할 데이터 서버로 넘기기

```swift
const onSubmit = (e) => {
        //원래 하려고 했던 것들을 방지 할 수 있다.
        e.preventDefault();

        //Video collection에 보내주기 위함.
        const variables = {
            writer: user.userData._id,
            title: VideoTitle,
            description: Description,
            privacy: Private,
            filePath: FilePath,
            category: Category,
            duration: Duration,
            thumbnail: ThumbnailPath, 
        }
				

			 //MongoDB에 저장하는 부분
        Axios.post('/api/video/uploadVideo',variables )
        .then(response => {
            if (response.data.success) {

                message.success('성공적으로 업로드를 했습니다.')

                //시간 뒤에 다시 메인 페이지로 돌아가기
                setTimeout(() => {
                    props.history.push('/')
                }, 3000);
                

            } else {
                alert('비디오 업로드에 실패하였습니다.')
            }
        })
    }
```

```swift
import { useSelector } from 'react-redux'

const user = useSelector(state => state.user);
```

- Redux 란?

> Javascript app을 위한 예측가능한(predictable) state container이다. 리액트 뿐만 아니라 Augular, jQuery, vanilla JavaScript 등 다양한 framework와 작동되게 설계되었다. 즉, 리액트만을 위한 Library는 아니다.

- React에 Redux 가 필요한 이유

Compoenet → Local State

Application → Global State

1. Local State 전달이 어렵다
- 프로젝트의 규모가 커지고 Compoenet의 수가 많아지게 되면, Props로 data를 전달하기 위해 필요없는 흐름이 생기게 됨.

 2. Global State의 유지가 어렵다.

- 유저의 인증정보들을 Props로 계속해서 전달해야 하는데, 복잡한 절차를 거치게 된다.

→ 하나의 store를 통해 global state을 포함한 모든 state를 저장, 유지 할 수 있게 되며, 원하는 Component로만 data를 전달 할 수 있게 됨

- Flux와 Redux

Flux : MVC구조의 단점을 보완할 목적으로 개발됨. 대규모 프로젝트에서 복잡해지는 MVC구조를 보완하는 단방향 데이터흐름의 구조

→ Redux는 Flux의 몇가지 중요한 특성에 영감을 받아 개발 됨.

- 차이점

Redux는 dispatcher의 개념이 없음

Multi store가 없으며, 대신 하나의 root에 하나의 store가 존재하며 순수함수에 의존하게 되는데 이 순수함수들은 이것들을 관리하는 추가적인 entity없이도 조합하기 쉬움

Flux 패턴을 좀 더 쉽고 정돈 된 형태로 쓸 수 있는 라이브러리 라고 볼 수 있음

- Redux 기본 개념

Action  :

어플리케이션의 store(스토어), 즉 저장소로 data를 보내는 방법이다. view에서 정의되어있는 액션을 호출하면 action creators(액션 생성자)는 어플리케이션의 state(상태)를 변경하여 준다.

Reducer : 

action을 통해 어떠한 행동을 정의했다면, 그 결과 어플리케이션의 상태가 어떻게 바뀌는지는 특정하게 되는 함수이다.

Store :

 “무엇이 일어날지”를 나타내는 action, 그리고 action에 따라 상태를 수정하는 reducer를 저장하는 어플리케이션에 있는 단 하나의 객체이다.

### UseSelector

- useSelector 는 리덕스의 상태값을 조회하기 위한 hook 함수로 이전의 connect 를 통해 상태값을 조회하는 것보다 훨씬 간결하게 작성하고 코드가독성이 상승되는 장점이 있는 함수입니다.

```swift
router.post("/uploadfiles", (req, res) => {

    upload(req, res, err => {
        if (err) {
            return res.json({ success: false, err })
        }
        return res.json({ success: true, filePath: res.req.file.path, fileName: res.req.file.filename })
    })

});
```

### 랜딩 페이지에 비디오 나타나게 하기!

1. 빈 랜딩 페이지 생성
2. 비디오 카드 Template 만들기
3. 몽고 DB에서 모든 비디오 데이터 가져오기
4. 가져온 비디오 데이터들을 스크린에 출력하기
5. Use map() methods

- 참고 : [https://medium.com/@jsh901220/react에-redux-적용하기-a8e6efd745c9](https://medium.com/@jsh901220/react%EC%97%90-redux-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0-a8e6efd745c9)
- [https://www.youtube.com/watch?v=nE5hkibAUzk&list=PL9a7QRYt5fqnlSRu--re7N_1Ean5jFsh3&index=9](https://www.youtube.com/watch?v=nE5hkibAUzk&list=PL9a7QRYt5fqnlSRu--re7N_1Ean5jFsh3&index=9)
