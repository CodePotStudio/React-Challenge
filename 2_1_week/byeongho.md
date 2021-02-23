# 1주차 - 20210221

# Boiler Plate 만들기

- express download

```jsx
npm install express --save
-> package.json 에 dependencies에 명시해라.
```

- express 공식 사이트 :

[Express - Node.js web application framework](https://expressjs.com/)

```jsx

const express = require('express') //module을 가져온다
const app = express() //새로운 express앱을 만든다
const port = 5000 // 5000번 서버를 백으로 둔다 

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
}) // listen을 하면 console에 출력 된다.
```

- mongoose → mongoDB

```jsx
npm install mongoose --save <mongoose 설치>
```

```jsx
"scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  }

npm run start 
만약에 실행할 js가 dev이면
npm run dev!
```

- Model : 스키마를 감싸주는것
- Schema : 하나하나 데이터의 정보를 저장 하는 것
- SSH 설정 : Secure shell , 안전하게 통신하기 위해서
- Body-Parser : Body 데이터를 분석(Parse)해서 req.body로 출력해주는 것.

    ```jsx
    const bodyParser = require('body-parser');
    //application/x-www-from-urlencoded ->이런 데이터들을 분석해서 가져오게 해줌
    app.use(bodyParser.urlencoded({extended:true})); 
    //application/json >이런 데이터들을 분석해서 가져오게 해줌
    app.use(bodyParser.json())
    ```

- Nodemon : 서버를 올리고 내리고 하지 않아도, 변화 된 것을 반영 함

```jsx
npm install nodemon --save-dev
//dev의 뜻 테스트 할때만 이거 쓰겠다

--> Dependencies가 아니라 dev~
"devDependencies": {
    "nodemon": "^2.0.6"
  }

"scripts": {
    "start": "node index.js",
    "backend" : "nodemon index.js", <-- 추가
    "test": "echo \"Error: no test specified\" && exit 1"
  }
```

- 비밀 정보 보안하기

Local 환경  - Development

Deploy 환경 - Production

환경에 따라 분기 처리 해야하 한다.

# bcrypt 사용하기 (암호화 된 비밀번호를 만들자)

- 설치

```jsx
npm install bcrypt --save
```

- bcrypt github

[https://github.com/kelektiv/node.bcrypt.js/](https://github.com/kelektiv/node.bcrypt.js/)

- 사용자 정보 변경

비밀 번호를 바꿀때만 암호화해야 한다.

```jsx
const mongoose = require('mongoose');

//1. bcrypt import
const bcrypt = require('bcrypt');

// salt를 이용해서 비밀번호를 암호화 해야 함
// saltRounds : salt가 몇글자 인지를 나타냄
const saltRounds = 10;

//2. mongoose method에서 pre사용 , 유저 정보를 저장하기전에 무엇을 하기 위한 작업 처리
userSchema.pre('save', function( next ) {
    //비밀 번호를 암호화 시킨다.

    var user = this;
    // this는 userSchema을 의미한다.

    if (user.isModified('password')) { //비밀 번호 변경 시에만 암호화를 해준다.
        bcrypt.genSalt(saltRounds,function(err,salt) {
            //salt 생성!
            if (err) return next(err)
            //user.save 쪽으로 돌려 보낸다.
    
    
            bcrypt.hash(user.password,salt,function(err,hash) {
                if (err) return next(err)
                //user.save 쪽으로 돌려 보낸다.
    
                //plain password를 hash된 비밀번호로 바꿔준다.
                user.password = hash
    
                next()
            })
        })
    }

})

const User = mongoose.model('User',userSchema)

module.exports = { User }
```

# 로그인 기능 만들기

1. 데이터 베이스에서 요청 된 이메일 찾기
2. 요청 한 이메일과 비밀번호가 같은지 확인
3. 비밀 번호 까지 맞다면 token을 생성한다.

## 필요한 라이브러리

jsonwebtoken jwt 라이브러리 설치

- 사이트 : [https://github.com/auth0/node-jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)

```jsx
npm install jsonwebtoken --save
```

쿠키에 저장하기 위해서는 cookie parser를 깔아야 한다.

- 사이트 : [https://github.com/expressjs/cookie-parser](https://github.com/expressjs/cookie-parser)

```jsx
npm install cookie-parser --save
```

## index.js

```jsx
app.post('/login' ,(req,res) => {
  //1.요청된 이메일을 데이터 베이스에서 있는지 찾는다.

  User.findOne({email : req.body.email}, (err,user) => {
    if (!user) {
      return res.json({
        loginSuccess : false,
        message : "제공 된 이메일에 해당하는 유저가 없습니다."
      })
    }
    //-> User.js 에 method 생성

    //3.요청된 이메일이 데이터 베이스에 있다면 비밀번호가 맞는 비밀번호 인지 확인
    user.comparePassword(req.body.password, (err,isMatch) => {
      if(!isMatch) 
      return res.json( {loginSuccess: false , message: "비밀번호가 틀렸습니다."})
    
  

  //4.비밀 번호 까지 맞다면 token을 생성한다.
      user.generateToken((err , user) => {
        if (err) return res.status(400).send(err);

        // 토큰을 저장한다. 어디에? 쿠키, 로컬 스토리지 , 세션  -> 어디에 저장 해야 안전한지에 대해서는 논란이 많다.

        // x_auth + cookie
        res.cookie("x_auth",user.token)
        .status(200) 
        .json({loginSuccess : true ,userId : user._id})     

      })

    }) 

  })

})
```

## user.js

```jsx

const jwt = require('jsonwebtoken');

//2. 비밀 번호 비교 : bcrypt로 암호화 된 비밀번호와 입력 된 비밀번호를 비교한다.
userSchema.methods.comparePassword = function(plainPassword, cb) {

    //plainPassword = 1234567 , 암호화 된 비밀 번호 : $123213dkaghghk
    bcrypt.compare(plainPassword, this.password, function(err,isMatch) {
        if (err) return cb(err);
        cb(null,isMatch);
    });

};

//5.토큰을 저장한다. 
userSchema.methods.generateToken = function(cb) {
    //json web token을 이용해서 token을 생성하기

    var user = this;

    var token = jwt.sign(user._id.toHexString(), 'secretToken')

    // user._id + 'secretToken' => token 생성 , token으로 누구인지 식별 가능
    user.token = token
    user.save(function(err,user) {
        if(err) return cb(err)
        cb(null,user)
    }) 

}
```

# 브라우저 저장소 종류와 보안 이슈

1. localStorage 저장 방식
- 브러우저 저장소에 저장하는 방식 , JavaScript 내 글로벌 변수로 읽기 / 쓰기 접근이 가능하다.

---

   취약점 : XSS 취약점을 통해 그 안에 담긴 값을 불러오거나, 불러온 값을 이용해 API콜을 위조 가능

2. 쿠키 저장 방식

- 브라우저 쿠키에 저장, 클라이언트 HTTP 요청을 보낼 때마다 자동으로 쿠키가 서버에 전송 된다. JavaScript 내 글로벌 변수로 읽기 / 쓰기 접근이 가능하다.

---

  취약점 : 

1. XSS 취약점을 통해 그 안에 담긴 값을 불러오거나, 불러온 값을 이용해 API콜을 위조 가능
2. 쿠키에 세션 id나 accessToken을 저장해 인증에 이용하는 구조에 CSRF 취약점이 있다면 인증 정보가 쿠키에 담겨 서버로 보내진다. 공격자는 유저 권한으로 정보를 가져오거나 액션을 수행할 수 있다.

---

   장 점 : 

쿠키에 refreshToken만 저장하고 새로운 accessToken을 받아와 인증에 이용하는 구조에서는 CSRF 취약점 공격을 방어할 수 있다. refreshToken으로 accessToken을 받아도 accessToken을 스크립트에 삽입할 수 없다면 accessToken을 사용해 유저 정보를 가져올 수 없기 때문이다.

3. secure httpOnly 쿠기 저장 방식

- 브라우저에 쿠키로 저장되는 건 같지만, Javascript 내에서 접근이 불가능하다. secure을 적용하면 https 접속에서만 동작한다.

---

취약점 :

1. 쿠키 저장 방식과 같은 이유 세션 id,accessToken은 저장 하면 안된다.
2. httpOnly 쿠키에 담긴 값에 접근 할 수 없지만 XSS 취약점을 노려 API 콜을 요청하면 httpOnly 쿠키에 담긴 값들도 함께 보내져 유저인척 정보를 빼오거나 액션을 수행 할 수 있다.

> 결론 : 어떤 저장 방식을 택해도 XSS 취약점이 있다면 보안이슈 존재, 유저 저장방식을 바꾸는 것만으로는 방어 불가능, 클라이언트와 서버에서 추가적으로 XSS 방어 처리가 필 수이다.

# 보안이 어떻게 뚫리는지?

1. XSS 공격

    공격자(해커)가 클라이언트 브라우저에 Javascript를 삽입해 실행하는 공격이다. 공격자가 `<input>`을 통해 Javascript를 서버로 전송해 서버에서 스크립트를 실행하거나, url에 Javascript를 적어 클라이언트에서 스크립트 실행이 가능하다면 공격자가 사이트에 스크립트를 삽입해 XSS 공격을 할 수 있다. 이때 공격자는 Javascript를 통해 사이트의 글로벌 변숫값을 가져오거나 그 값을 이용해 사이트인 척 API 콜을 요청할 수도 있다. 다시 말하면 공격자의 코드가 내 사이트의 로직인 척 행동할 수 있다는 거다.

2. CSRF 공격

    공격자가 다른 사이트에서 우리 사이트의 API 콜을 요청해 실행하는 공격이다. API 콜을 요청할 수 있는 클라이언트 도메인이 누구인지 서버에서 통제하고 있지 않다면 CSRF가 가능한데, 이때 공격자가 클라이언트에 저장된 유저 인증정보를 서버에 보낼 수 있다면, 제대로 로그인한 것처럼 유저의 정보를 변경하거나 유저만 가능한 액션들을 수행할 수 있다. 예를 들어 CSRF에 취약한 은행 사이트가 있다면 로그인한 척 계좌 비밀번호를 바꾸거나 송금을 보낼 수 있는 것이다.

React에서(= 클라이언트) 위 프로세스들을 따라 세션 `id`나 `accessToken` 같은 인증정보를 저장할 때 (인증 방식 4번 단계 해당) 이용하는 저장소는 보통 `localStorage`나 쿠키다. 페이지를 리프레시 하거나 창을 닫고 다시 접속할 때도 로그인 정보가 이어지도록 둘 다 브라우저에 저장하는 방식이다. 하지만 두 방식은 XSS와 CSRF 공격에 취약할 수 있다.

---

> 결론

1. JWT로 유저 인증
2. refreshToken → secure httpOnly 쿠키 , access Token은 JSON payload로 받아와서 웹 어플리케이션 내 로컬 변수로 이용
3. 이를 통해 CSRF 취약점 공격 방어하고 , XSS 취약점 공격으로 저장된 유저 정보 읽기는 막을 수 있음
4. XSS 취약점을 통해 API 콜을 보낼 때는 무방비, XSS 자체를 막기 위해 서버와 클라이언트가 모두 노력해야 함.

### 클라이언트에서 처리하기

먼저, React 최상단 index.js에서 axios에 `withCredentials`를 `true`로 설정해줘야 `refreshToken` cookie를 주고받을 수 있다.

```jsx
import React from "react";
import ReactDOM from "react-dom";
import axios from "axios";

import App from "./App";

axios.defaults.baseURL = "https://www.abc.com";
axios.defaults.withCredentials = true;

onLogin = (email, password) => {
	const data = {
		email,
		password,
	};
	axios.post('/login', data).then(response => {
		const { accessToken } = response.data;

		// API 요청하는 콜마다 헤더에 accessToken 담아 보내도록 설정
		axios.defaults.headers.common['Authorization'] = `Bearer ${accessToken}`;

		// accessToken을 localStorage, cookie 등에 저장하지 않는다!

	}).catch(error => {
		// ... 에러 처리
	});
}
```

참고 : 

1. [https://velog.io/@yaytomato/프론트에서-안전하게-로그인-처리하기](https://velog.io/@yaytomato/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%90%EC%84%9C-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0)
2. [https://www.youtube.com/watch?v=fgoMqmNKE18&list=PL9a7QRYt5fqkZC9jc7jntD1WuAogjo_9T](https://www.youtube.com/watch?v=fgoMqmNKE18&list=PL9a7QRYt5fqkZC9jc7jntD1WuAogjo_9T)

      따라하면서 배우는 리액트 (강의)