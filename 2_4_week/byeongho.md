## Youtube clone coding 하기

[https://www.youtube.com/watch?v=fM0Vj7dBcm8&list=PL9a7QRYt5fqnlSRu--re7N_1Ean5jFsh3&index=1](https://www.youtube.com/watch?v=fM0Vj7dBcm8&list=PL9a7QRYt5fqnlSRu--re7N_1Ean5jFsh3&index=1)

### 시작하게 된 이유

→ 지난 시간 까지는 Twitter clone coding을 하면서, 노마드 코더의 강의를 보았는데, 강의 자체가 너무 빠르고 설명의 비약이 좀 많다는 생각이 들었고 개인적으로는 비유해주는 것도 알맞지 않았다. 그래서 예전에 Boiler Plate 강의를 들었었던 , Jhon ahn님의 강의를 듣게 되었다. 

dependencies → Library라고 생각하면 됩니다.

npm i react-scripts

## MONGO DB 연결

- 노드 버전 확인 : node -v
- npm install → dependencies가 자동으로 설치 됨.

```jsx
<key.js> 프로덕션, dev 모드 분기

if (process.env.NODE_ENV === 'production') {

module.exports = require('./prod');

} else {

module.exports = require('./dev');

}

1. production → 
2. dev → localHost에서의 작업
```

- MongoDB 로그인

![4%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209cda94f31eec4d4fa9c4ca19e03c364f/_2020-12-25__4.01.57.png](4%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209cda94f31eec4d4fa9c4ca19e03c364f/_2020-12-25__4.01.57.png)

![4%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209cda94f31eec4d4fa9c4ca19e03c364f/_2020-12-25__4.03.20.png](4%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209cda94f31eec4d4fa9c4ca19e03c364f/_2020-12-25__4.03.20.png)

- Add New Database User에서 username과 password를 가져온다.

## Upload Video 페이지

```jsx
"scripts": {
    "start": "node server/index.js", //서버 가동
    "backend": "nodemon server/index.js", //nodemon 이용 -> save 할때 마다 변환된 코드를 인식해서 보여줌
    "frontend": "npm run start --prefix client", // client 부분 가동 ; 두번기동 ?? 
    "dev": "concurrently \"npm run backend\" \"npm run start --prefix client\"" // concurrently 을 이용해서 같이 기동 할 수 있게 하는 라이브러리
  },
```

- react drop zone 설치

```swift
npm install react-dropzone --save
```

값을 여러개 선택해야 할때는 map을 이용하자

map은 index가 필수적으로 있어야 한다.

```jsx

//value 2개 일때
const PrivateOptions = [
        {value : 0 , label : "Private"},
        {value : 1 , label : "Public"}
    ]

//value 5개 일때
const CategoryOptions = [
        {value : 0 , label : "Film & Animation"},
        {value : 1 , label : "Auto & Vehicles"},
        {value : 2 , label : "Music"},
        {value : 3 , label : "Pets & Animals"}
    ]

<select onChange>

                            {PrivateOptions.map((item,index) => (
                                <option key={index} value={item.value}>{item.label}</option>
                            ))}
                    

 </select>

```

- State를 변화시키려면 Onchage 함수를 추가한다.

```jsx
const onTitleChange = (e) => {
        setVideoTitle(e.currentTarget.value)
    }

<Input
                    onChange={onTitleChange}
                    value={VideoTitle}
                />
```

### Multer로 서버에 비디오 저장하기

: node.js의 라이브러리인데 , 파일 업로드 api를 쉽게 구축 할 수 있게 도와 줌.

1. OnDrop func 만들기 (Client)

```jsx
const onDrop = (files) => {
        let formData = new FormData;
        const config = {
            header : {'content-type' : 'multipart/form-data'}
        }

        formData.append("file",files[0])
        //서버에 req할때 axios를 사용하는데, formdata를 같이 보내지 않으면
        //파일을 보낼때는 에러가 생긴다.

        console.log(files)

        Axios.post('/api/video/uploadfiles', formData, config)
        .then(response => {
            if (response.data.success) {
                console.log(response.data)
            } else {
                alert('비디오 업로드를 실패했습니다.')
            }
        })

    }
```

```jsx
<Dropzone
                        onDrop={onDrop}
                        //몇개의 파일으 올릴 건지?
                        multiple={false}
                        //맥스 사이즈 제한
                        maxSize={1000000000}
                        >
                        {({ getRootProps, getInputProps}) => (
                            <div style={{ width : '300px' , height : '240px' , border : '1px solid lightgray' , display: 'flex',
                            alignItems:'center' , justifyContent:'center'}} {...getRootProps()}>
                                <input {...getInputProps()} />
                                <Icon type="plus" style={{ fontSize : '3rem'}} />
                            </div>

                        )}
                        </Dropzone>
```

### Node.js 쪽 구성하기 (back-end)

2.

- index.js에서 video route를 추가한다

```jsx
app.use('/api/video', require('./routes/video'));
```

- multer를 설치해 준다.

```jsx
 npm install multer --save
```

- Video.js 환경 구성
1. diskStorage 로 저장 받는 파일의 장소와 저장하게될 파일 이름을 설정.

 2.  destination 필드에 파일 다운 받을 경로를 정하고 filename 필드에 다운 받는 파일 이름을 정한다.

 3.  정해둔 값으로 multer 오브젝트를 생성한다.

```jsx
const express = require('express');
const router = express.Router();
//const { Video } = require("../models/User");

const { auth } = require("../middleware/auth");
const { response } = require('express');
const multer = require("multer");

const now = Date.now();

//=================================
//             Video
//=================================

//Storage MULTER CONFIG
let storage = multer.diskStorage({
    destination: (req,file,cb) => {
        //uploads라는 폴더에 저장한다.
        cb(null, "uploads/");
    },
    

    filename :(req,file,cb) => {
        // 날짜 + filename으로 저장
        cb(null,'동영상 이름' );
    },

    fileFilter : (req,file,cb) =>  {
        const ext = path.extname(file.originalname)
        // mp4만 받을 수 있다.
        if (ext !== '.mp4') {
            return cb(res.status(400).end('only mp4 is allowed'), false);
        }
        cb(null, true)
    }

});
    
const upload = multer({storage : storage}).single("file");

router.post('/uploadfiles', (req, res) => {
    //비디오를 서버에 저장 한다.
    upload(req, res , err => {

        if (err) {

            return res.json({ success : false , err})
        }

        return res.json({success: true, url: res.req.file.path, fileName: res.req.file.filename})
    })

})

module.exports = router;
```

## ffm로 비디오 썸네일 만들기

1. ffmpeg 설치

```jsx
brew install ffmpeg
```

2. fluent-ffmpeg설치

```jsx
npm install fluent-ffmpeg --save
```

3. ffmpeg를 시용해서 thumbnail을 서버에 저장 하기

```jsx

router.post('/thumbnail', (req, res) => {

    // 썸네일 생성하고 비디오 러닝 타임도 가져오기
    let filePath = ""
    let fileDuration = ""

    //비디오 정보 가져오기

    ffmpeg.ffprobe(req.body.url, function (err, metadata) {
        console.dir(metadata); //모든 메타 데이터
        console.log(metadata.format.duration);
         fileDuration = metadata.format.duration
    });

    //썸네일 생성

    //클라이언트에서 가져온 비디오 저장 경로
    ffmpeg(req.body.url)

    //비디오 파일 name을 생성 한다.
    .on('filenames', function (filenames) {
        console.log('will generate ' + filenames.join(', '))
        console.log(filenames)

        filePath = "uploads/thumbnails/" + filenames[0]
    })

    // 썸네일을 생성한 후 할 작업.
    .on('end', function() {
        console.log('Screenshots taken');
        //-> 성공
        return res.json({
             success : true ,
              url : filePath ,
              fileDuration : fileDuration});
    })

    //에러가 났을 시 에 작업
    .on('error', function (err) {
        console.error(err);
        //-> 실패
        return res.json({ success : false , err });
    })

    // 옵션
    .screenshot({
        
        count : 3,
        //업로드 폴더안에 썸네일 폴더를 생성해줘야 한다.
        folder : 'uploads/thumbnails',
        size: '320x240',
        filename : 'thumbnail-%b.png'

    })

})
```
