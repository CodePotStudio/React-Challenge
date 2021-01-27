# create-react-app으로 시작하기

- create-react-app은 리액트로 웹 애플리케이션을 만들기 위한 환경을 제공합니다.
- create-react-app을 이용하게 된다고 한다면 기존 기능을 개선하거나 또는 새로운 기능을 추가했을 때 패키지 버전을 올리면 된다

# create-react-app 사용해보기 

```
npx create-react-app cra-test

```
- npm 버전이 낮아서 실행이 되지 않는다면 다음과 같이 입력합니다.

```
npm install -g create-react-app
create-react-app cra-test

```

- 웹페이지를 띄워보는 방법은 다음과 같다.

```
cd cra-test
npm start

```
- 빌드가 끝나게 되면 자동으로 브라우저에서 새 탭이 열린다.
- create-react-app에서 자동으로 생성된 파일을 살펴보자면 다음과 같다.

cra-test<br>
|___README.md<br>
|___node_modules<br>
|___package.json<br>
|___public<br>
|   |___favicon.ico<br>
|   |___index.html<br>
|___src<br>
    |___App.css<br>
    |___App.js<br>
    |___App.test.js<br>
    |___index.css<br>
    |___index.js<br>
    |___logo.svg<br>   
    |___serviceWorker.js

- `index.html,index.js` 파일은 빌드시 예약된 파일 이름이기 때문에 지우면 안된다.
- `index.html,index.js,package.json` 파일을 제외한 나머지 파일은 데모 앱을 위한 파일이기 때문에 수정하거나 삭제해도 괜찮다.
- `index.js`로부터 연결되어있는 모든 자바스크립트 파일과 CSS파일은 src 폴더 밑에 있어야 한다. (src 폴더 바깥에 있는 파일은 import 키워드를 이용해서 가져오려고 하면 실패한다.)
- index.html 에서 참조하는 파일은 public 폴더 밑에 있어야 한다. 또 자바스크립트 파일이나 CSS 파일을 link나 script 태그를 이용해 포함할 수 있다.
- src 폴더 밑에서 import키워드를 사용해서 포함시키는 것이 좋다.
- index.html의 내용을 직접 수정해도 괜찮지만 제목을 페이지별로 다르게 줘야 한다고 한다면 문제가 될 수 있다.
- 사내에서 쓰이는 웹사이트라 한다면 react-helmet과 같은 패키지를 사용하면 된다.
- 검색 엔진 최적화가 중요하다고 한다면 서버사이드 렌더링에 특화된 넥스트(next.js)를 사용하는 것이 좋다.
- serviceWorker.js파일에는 PWA(progressive web app)과 관련된 코드가 들어있다.
- PWA는 오프라인에도 잘 동작하는 웹 애플리케이션을 만들기 위한 기술이다. 기본적으로 create-react-app에서는 꺼져 있는 상태이다.
- create-react-app에 PWA기능을 원한다고 한다면 index.js파일에 serviceWorker.register(); 코드를 넣으면 된다.


## 주요 명령어 알아보기

- package.json파일을 열어보게 되면 npm 스크립트 명령어를 확인할 수 있다. 
- `npm start`는 개발모드로 프로그램을 실행하는 명령어이다. 
- 개발모드로 실행하면 HMR이 동작하기 때문에 코드를 수정하면 화면에 즉시 반영된다.
- HMR이 없다면 코드를 수정하고 브라우저에서 수동으로 새로고침을 해야 하므로 번거롭다.
- 에러 메시지가 출력된 영역을 클릭하게 되면 에러가 발생한 파일이 에디터에서 열린다.
- 때에 따라서 API 호출을 위해서 https로 실행해야 할 수 있는데 create-react-app에서는 기본적으로 실행하는 옵션을 제공합니다.

```
맥: HTTPS = true npm start
윈도우 : set HTTPS = true && npm start
```

- 이 명령어를 실행하게 되면 서명된 인증서와 함께 http 사이트로 접속된다.

## 빌드하기

- npm run build 명령어는 배포 환경에서 사용할 파일을 만들어준다.
- 빌드 후 생성된 자바스크립트 파일과 CSS 파일을 열어보면 사람이 읽기 힘든 형식으로 압축된 것을 확인할 수 있다.

```
npx serve -s build

```

- serve 패키지는 노드 (node.js) 환경에서 동작하는 웹 서버 애플리케이션이다.
- `build/static` 폴더 밑에 생성된 파일의 이름에 해시값이 포함되어 있다.
- 파일의 내용이 변경되지 않으면 해시값은 항상 같다.
- 새로 빌드를 하더라도 변경되지 않은 파일은 브라우저에 캐싱되어 있는 파일들이 사용된다.
- 재방문의 경우 빠르게 페이지가 렌더링 되는 효과를 볼 수 있다.
- 자바스크립트 파일에서 import 키워드를 이용해서 가져온 CSS 파일은 다음 경로에 저장합니다.


```

bulid/static/css/main.{해시값}.chunk.css

```

- 여러개의 CSS 파일을 임포트하더라도 모두 앞의 파일에 저장된다.
- 자바스크립트 파일에서 import 키워드를 이용해서 가져온 폰트,이미지 등의 리소스 파일들은 bulid/static/media 폴더 밑에 저장된다.

```
//...
// 1
import smallImage from './small.jpeg';
import bigImage from './big.jpeg';

function App() {
    return (
        <div className-"App">
        // 2
            <img src={bigImage} alt ="big" />
            <img src={smallImage} alt ="small" />
        // ...
    )
}

```
- 1 : 일반적인 자바스크립트 모듈처럼 이미지 파일을 자바스크립트로 가져오게 됩니다.
- 2 : bigImage,smallImage 변수는 해당 이미지의 경로를 나타내는 문자열이다.
- media 폴더에는 `big.{해시값}.jpeg` 파일이 생성된다.
- `small.{해시값}.jpeg` 파일은 생성되지 않는다. 해당 파일은 main.{해시값}.js 파일에서 data:image 키워드를 검색해본다.
- 이미지 파일이 문자열 형태로 자바스크립트 파일에 포함되어 있다.