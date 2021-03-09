# JSX와 Babel
- Babel : javascript에서 html 문법을 사용할 수 있도록 함.
- 자바스크립트 컴파일러
- ES6, ES7 등의 최신 문법을 브라우저가 이해할 수 있는 문법으로 변환하는 역할
- 컴포넌트를 재사용할 수 있기때문에 편함 
- 대문자로 시작하는 것 === 컴포넌트
- React에서 ES6문법을 사용함
- 세부설정을 하려면 webpack이 필요함
- 최신 객체나 메서드는 babel/polyfill을 추가해야 함
- state : 변할 수 있는 부분
  
```javascript
render() {
            return <button type="submit" onClick={()=> {this.setState({liked:true})}}>
               {this.state.liked? 'Liked':'Like'}
              </button>
        } 

```

# webpack 
- webpack이란 최신 프론트엔드 프레임워크에서 가장 많이 사용하는 자바스크립트 정적 모듈 번들러입니다.
- 모듈러란 HTML, CSS, Javascript 파일을 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 것을 말합니다.


### 특징

- Entry : 의존성 그래프의 시작점
- Output : 번들된 결과물을 처리할 위치를 output(dist)에 위치합니다.
- Loader : 웹팩은 JavaScript와 Json만 이해할 수 있습니다. 따라서 Loader는 웹팩이 이해하고 처리 가능한 모듈로 변환시키는 역할을 수행합니다.
- Plugin : 번들된 결과물을 처리하는 역할

### webpack 세팅 
- 1. 먼저 webpack을 실행하기 위해서 다음의 명령어로 webpack을 세팅해줍니다.
  
```
npm i -D webpack webpack-cli

// webpack : webpack을 사용하기 위해서 설치하는 모듈
// webpack-cli : 콘솔에서 webpack을 사용하기 위해서 설치하는 모듈 

```

- 
2. webpack.config.js 파일을 같은 디렉터리 내에서 생성합니다.

- name : module의 이름
- mode : 모드 설정(개발 모드일지, 배포 모드일지)
- devtool : 개발을 용이하게 하기 위해서 소스맵을 제공함
- resolve : entry의 확장자를 사용하지 않기 위해서 설정하는 옵션
- entry : 진입점, 필요한 모듈을 로딩하고 하나로 묶습니다.
- module : rule을 정해서 각 확장자마다 loader를 추가할 수 있습니다.
- output : 번들된 결과물을 처리할 위치를 output(dist)에 위치합니다.

아래의 소스를 작성해줍니다.

```javascript
const path = require('path');

module.exports = {
  name:'wordrelay-setting',
  mode:'development', // 실서비스 : production
  devtool: 'eval',
  resolve: {
    extensions: ['.js', '.jsx']
  },
  
  entry: {
     app:['./client', './WordRelay'],
  }, // 입력
  module: {
    rules:[{
      test:/\.jsx?/,
      loader: 'babel-loader',
      options:{
        presets:[
           '@babel/preset-env',
           '@babel/preset-react'
        ],
        plugins: ['@babel/plugin-proposal-class-properties']
      }
    }],  
  },

  output:{
    path: path.join(__dirname, 'dist'),
    filename: 'app.js'
  }, // 출력
};

```

- 모듈을 설치합니다.

```
@babel/core : 최신문법을 바꿔주는것
@babel/preset-env : 브라우저에 필요한 ecmascript 버전을 자동으로 파악해서 알아서 polyfill을 넣어줍니다.
@babel/preset-react : react를 설치하신 분은 필요한 모듈
bable-loader : webpack과 babel을 이어주는 역할 
```
