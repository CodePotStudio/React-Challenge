## import와 export

# 모듈

- `React`의 파일을 여러 개로 나누게 되는데 이 때 분리된 파일의 이름을 모듈(module)이라고 부릅니다.
- 모듈 내에 있는 값들을 외부로 내보내고 다른 모듈에서 해당 모듈의 값들을 불러오기도 가능합니다.
- 이것을 `export`(내보내기),`import`(가져오기)라고 부릅니다.

- `AMD`: 가장 오래된 모듈 시스템중 하나로 require.js라는 라이브러리를 통해 처음 개발되었습니다.
- `CommonJS` : Node.js 서버를 위해서 만들어진 모듈 시스템입니다.
- `UMD` : AMD와 CommonJS와 같은 다양한 모듈 시스템을 함께 사용하기 위해 만들어졌습니다.

```jsx
// App.js
import { useState } from "react";
```

- 우리가 만들었던 `App.js`를 보면 위와 같은 코드를 보았을 껍니다.
- `react.js` 모듈에서 `useState`이름을 가진 변수 혹은 함수를 가져온다는 뜻입니다.

```jsx
// react.js
export function useState(...){
    ...
}

```
- `useState`함수를 선언하고 함수 앞에 `export` 예약어를 붙여주었습니다. 그래서 다른 파일에 손쉽게 `useState`에 접근할 수 있습니다.

- `sayHi.js`의 내부 함수 sayHi를 외부로 내보내봅시다.   

```jsx
// 📁 sayHi.js
export function sayHi(user) {
  alert(`Hello, ${user}!`);
}
```
- 이제 import 지시자를 사용해서 main.js 함수에서 함수 sayHi를 사용할 수 있게 해봅시다.

```jsx
// 📁 main.js
import {sayHi} from './sayHi.js';

alert(sayHi); // 함수
sayHi('John'); // Hello, John!
```

- 이것을 브라우저에서 모듈이 어떻게 동작하는지 알아봅시다.

```jsx
<!doctype html>
<script type="module">
  import {sayHi} from './say.js';

  document.body.innerHTML = sayHi('John');
</script>
```

## 모듈의 핵심기능

# 엄격 모드로 실행됨

- 모듈은 항상 엄격모드 (use strict)으로 실행됩니다. 선언되지 않는 변수에 값을 할당하는 등의 코드는 에러를 발생시킵니다.

```jsx
<script type="module">
  a = 5; // 에러
</script>
```

# 모듈 레벨 스코프

- 모듈은 자신만의 스코프가 있습니다. 그래서 모듈 내부에서 정의한 변수나 함수는 다른 스크립트에서 접근할 수 없습니다.
- 외부에 공개하려는 모듈은 export해야하고 내보내진 모듈은 가져와서 사용할려면 import를 해야합니다.
- 브라우저 환경에서도 `<script type="module">`을 사용해 모듈을 만들면 독립적인 스코프가 만들어집니다.

```jsx
<script type="module">
  // user는 해당 모듈 안에서만 접근 가능합니다.
  let user = "John";
</script>

<script type="module">
  alert(user); // Error: user is not defined
</script>
```
- 브라우저 환경에서 부득이하기 window 레벨 전역 변수를 만들어야 한다면 window객체에 변수를 명시적으로 할당해야 합니다.
- window.user와 같이 접근하는 방식을 취하면 되지만 꼭 필요한 경우에만 사용합니다.

# 단 한번만 평가됨
- 동일한 모듈이 여러 곳에서 사용되더라도 최초 호출시 한번만 실행됩니다.
- 실행 후 결과는 모듈을 가져가려는 모든 모듈에서 내보내 집니다.

```jsx
// 📁 alert.js
alert("모듈이 평가되었습니다!");
```

```jsx
// 동일한 모듈을 여러 모듈에서 가져오기

// 📁 1.js
import `./alert.js`; // 얼럿창에 '모듈이 평가되었습니다!'가 출력됩니다.

// 📁 2.js
import `./alert.js`; // 아무 일도 발생하지 않습니다.- 단 한번만 평가됨
```

```jsx
// 객체를 내보내는 모듈
// 📁 admin.js
export let admin = {
  name: "John"
};
```
- 모듈을 가져오는 모듈이 여러 개이더라도 앞서서 설명한 것처럼 모듈은 최초 호출 시 단 한 번만 평가됩니다.
- 이 때 `admin`객체가 만들어지고 이 모듈을 가져오는 모든 모듈에 `admin` 객체가 전달됩니다.

```jsx
// 📁 1.js
import {admin} from './admin.js';
admin.name = "Pete";

// 📁 2.js
import {admin} from './admin.js';
alert(admin.name); // Pete

// 1.js와 2.js 모두 같은 객체를 가져오므로
// 1.js에서 객체에 가한 조작을 2.js에서도 확인할 수 있습니다.
```

```jsx
// 📁 admin.js
// 외부에서 admin 객체와 관련된 인증 정보를 받아와야 함
export let admin = { };

export function sayHi() {
  alert(`${admin.name}님, 안녕하세요!`);
}
```

```jsx
// 📁 init.js
import {admin} from './admin.js';
// 최초로 실행되는 init.js에서 admin.name을 설정하면 
// admin.js를 포함한 외부 스크립트에서 admin.name에 저장된 정보를 볼 수 있다.
admin.name = "Pete";
```

```jsx
// 📁 other.js
import {admin, sayHi} from './admin.js';

alert(admin.name); // Pete
// admin.name에 저장된 정보를 볼 수 있다.

sayHi(); // Pete님, 안녕하세요!
```

# import.meta

- `import.meta`객체는 현재 모듈에 대한 정보를 제공해줍니다.
- 호스트 환경에 따라서 제공하는 정보의 내용은 다르지만 브라우저 환경에서는 스크립트의 url정보를 얻을 수 있습니다.
- HTML 안에 있는 모듈이라면 현재 실행 중인 웹페이지의 url정보를 얻을 수 있습니다.

```jsx
<script type="module">
  alert(import.meta.url); // script URL (인라인 스크립트가 위치해 있는 html 페이지의 URL)
</script>
```

# 'this'는 undefined

- 모듈 최상위 레벨의 `this`는 `undefined`입니다.
```jsx

<script>
  alert(this); // window
</script>

<script type="module">
  alert(this); // undefined
</script>

```

## 브라우저의 특정 기능

- 브라우저 환경에서  `type="module"`이 붙은 스크립트가 일반 스크립트와 어떤 점이 다른지 알아봅시다.

# 지연 실행

- 모듈 스크립트는 아래와 같은 특징이 있습니다.
- 외부 모듈 스크립트 `<script type="module" src="...">`를 다운로드할 때 브라우저의 HTML 처리가 멈추지 않습니다. 
- 모듈 스크립트는 HTML 문서가 완전히 준비될 때까지 대기 상태에 있다가 HTML 문서가 완전히 만들어진 이후에 실행됩니다. 
- 스크립트의 상대적 순서가 유지가 됩니다. (위쪽 스크립트부터 차례로 실행)

```jsx
<script type="module">
  alert(typeof button); // 모듈 스크립트는 지연 실행되기 때문에 페이지가 모두 로드되고 난 다음에 얼럿 함수가 실행되므로
  // 얼럿창에 object가 정상적으로 출력됩니다. 모듈 스크립트는 아래쪽의 button 요소를 '볼 수' 있죠.
</script>

하단의 일반 스크립트와 비교해 봅시다.

<script>
  alert(typeof button); // 일반 스크립트는 페이지가 완전히 구성되기 전이라도 바로 실행됩니다.
  // 버튼 요소가 페이지에 만들어지기 전에 접근하였기 때문에 undefined가 출력되는 것을 확인할 수 있습니다.
</script>

<button id="button">Button</button>
```
# 인라인 스크립트의 비동기 처리

- 모듈이 아닌 일반 스크립트에서 `async`속성은 외부 스크립트를 불러올 때만 유효합니다.
- `async`속성이 붙은 스크립트는 로딩이 끝나면 다른 스크립트나 HTML 문서가 처리되길 기다리지 않고 바로 실행됩니다.
- 모듈 스크립트에서는 `async`속성을 인라인 스크립트에서도 적용할 수 있습니다.
- 이와 같은 속성은 어디에서도 종속되지 않는 기능을 구현할 때 유용하게 사용됩니다.

```jsx
<!-- 필요한 모듈(analytics.js)의 로드가 끝나면 -->
<!-- 문서나 다른 <script>가 로드되길 기다리지 않고 바로 실행됩니다.-->
<script async type="module">
  import {counter} from './analytics.js';

  counter.count();
</script>
```

# 외부 스크립트

- type="module"가 붙은 외부 모듈 스크립트엔 두 가지 큰 특징이 있습니다.
1. src 속성값이 동일한 외부 스크립트는 한 번만 실행됩니다.

```jsx
<!-- my.js는 한번만 로드 및 실행됩니다. -->
<script type="module" src="my.js"></script>
<script type="module" src="my.js"></script>
```

2. 외부 사이트같이 다른 오리진에서 모듈 스크립트를 불러오려면 fetch와 Cross-Origin 요청 챕터에서 설명한 바와 같이 CORS 헤더가 필요합니다. 
```jsx
<!-- another-site.com이 Access-Control-Allow-Origin을 지원해야만 외부 모듈을 불러올 수 있습니다.-->
<!-- 그렇지 않으면 스크립트는 실행되지 않습니다.-->
<script type="module" src="http://another-site.com/their.js"></script>
```

# ‘경로가 없는’ 모듈은 금지

- 브라우저 환경에서 `import`는 반드시 상대 혹은 절대 URL앞에 와야 합니다.
- 경로가 없는 모듈은 허용하지 않습니다.

```jsx
import {sayHi} from 'sayHi'; // Error!
// './sayHi.js'와 같이 경로 정보를 지정해 주어야 합니다.
```

# 호환을 위한 ‘nomodule’

- 구식 브라우저는 type="module"을 해석하지 못하기 때문에 모듈 타입의 스크립트를 만나면 이를 무시하고 넘어갑니다. nomodule 속성을 사용하면 이런 상황을 대비할 수 있습니다.

```jsx
<script type="module">
  alert("모던 브라우저를 사용하고 계시군요.");
</script>

<script nomodule>
  alert("type=module을 해석할 수 있는 브라우저는 nomodule 타입의 스크립트는 넘어갑니다. 따라서 이 alert 문은 실행되지 않습니다.")
  alert("오래된 브라우저를 사용하고 있다면, type=module이 붙은 스크립트는 무시합니다. 대신 이 alert 문이 실행됩니다.");
</script>
```

# 빌드 툴

- 브라우저 환경에서 모듈을 단독으로 사용하는 일은 흔하지 않습니다.
- 대개 웹팩(Webpack)과 같은 특별한 툴을 사용해서 모듈을 한 데 묶어(번들링) 프로덕션 서버에 올리는 방식을 사용합니다.
- 번들러를 사용하면 모듈 분해를 통제할 수 있습니다.
- 경로가 없는 모듈이나 CSS,HTML 포맷의 모듈을 사용할 수 있게 해준다는 장점이 있습니다.

1. HTML의 `<script type="module">`에 넣을 ‘주요’ 모듈(‘진입점’ 역할을 하는 모듈)을 선택합니다.
2. ‘주요’ 모듈에 의존하고 있는 모듈 분석을 시작으로 모듈 간의 의존 관계를 파악
3. 모듈 전체를 한데 모아 하나의 큰 파일을 만듭니다(설정에 따라 여러 개의 파일을 만드는 것도 가능합니다). 이 과정에서 import문이 번들러 내 함수로 대체되므로 기존 기능은 그대로 유지됩니다
4. 변형이나 최적화도 함께 수행됩니다.
- 도달 가능하지 않은 코드는 삭제
- 내보내진 모듈 중 쓰임처가 없는 모듈을 삭제
- console, debugger 같은 개발 관련 코드가 삭제
- 자바스크립트 문법이 사용된 경우 바벨(Babel)을 사용하여 같은 기능을 하는 낮은 버전의 스크립트로 변환
- 공백 제거, 변수 이름 줄이기 등으로 산출물의 크기를 줄입니다.

```jsx
<!-- 웹팩과 같은 툴로 번들링 과정을 거친 스크립트인 bundle.js -->
<script src="bundle.js"></script>
```
## 모듈 내보내기 / 가져오기

# 내보내기

- 모듈을 내보내는 함수와 변수 앞에 `export`를 아래와 같이 붙여주면 됩니다.

```jsx
// sample1.js
export const fruits = ["사과", "감", "배"];
export function sayHello() {
    console.log("hello");
}
export const sayHi = () => console.log("hi");
```

- `export`를 붙여주는것이 어렵다면 아래와 같이 할 수 있습니다. (동일하게 작동)

```jsx
const fruits = ["사과","감","배"];
function sayHello() {
    console.log("hello");
}
const sayHi = () => console.log("hi");
export {fruits,sayHello,sayHi};
```

# 가져오기

- `sample1.js`에서 변수와 함수를 가져와 보도록 하겠습니다.

```jsx
// sampleImport.js
import { fruits, sayHello.sayHi } from "sample1.js";
```
- 필요로 하는 함수를 `import{...}` 괄호 안에 넣어주면 됩니다.
- `from`뒤에는 실제 파일이 존재하는 경로를 넣어줍니다.

```jsx
// sampleImport.js
import { fruits, sayHello, sayHi } from "sample1";
```

- 가져온 함수나 변수의 이름을 바꾸고 싶다면 as라는 예약어를 사용하면 됩니다.
- import나 export에서 사용이 가능합니다.

```jsx
// sampleImport.js
import { fruits as fruitsName, sayHello, sayHi } from "sample1";

console.log(fruitsName);
// '사과', '감', '배'
```
# export default

- 내보내는 방법 중에서는 `default`문법이 있습니다.
- 해당 모듈에서 함수나 변수를 기본적으로 내보내겠다는 뜻입니다.

```jsx
// fruits.js
export default fruitNames = ["사과", "감", "배"];
```
- `default`로 내보내기를 하게 되면 `import`를 할 때 `{}`없이 `import`가 가능합니다.
```jsx
// fruitsImport.js
import fruitNames from "fruits";
```

# default export의 이름에 관한 규칙

- named export는 내보냈을 때 사용한 이름 그대로 가져오므로 관련 정보를 파악하기 쉽습니다.
-  쓴 이름과 가져오기 할 때 쓸 이름이 동일해야 한다는 제약이 있습니다.

```jsx
import {User} from './user.js';
// import {MyUser}은 사용할 수 없습니다. 반드시 {User}이어야 합니다.
```
- named export와는 다르게 default export는 가져오기 할 때 개발자가 원하는 대로 이름을 지정해 줄 수 있습니다.

```jsx
import User from './user.js'; // 동작
import MyUser from './user.js'; // 동작
// 어떤 이름이든 에러 없이 동작합니다.
```

- 코드의 일관성을 유지하기 위해 default export 한 것을 가져올 땐 아래와 같이 파일 이름과 동일한 이름을 사용하도록 팀원끼리 내부 규칙을 정할 수 있습니다.

```jsx
import User from './user.js';
import LoginForm from './loginForm.js';
import func from '/path/to/func.js';
...
```
## 모듈 다시 내보내기

- `export ... from ...` 문법을 사용하면 가져온 개체를 즉시 ‘다시 내보내기(re-export)’ 할 수 있습니다. 

```jsx
export {sayHi} from './say.js'; // sayHi를 다시 내보내기 함

export {default as User} from './user.js'; // default export를 다시 내보내기 함
```

```
// 패키지 구조
auth/
    index.js
    user.js
    helpers.js
    tests/
        login.js
    providers/
        github.js
        facebook.js
        ...
```
```jsx
// 📁 auth/index.js

// login과 logout을 가지고 온 후 바로 내보냅니다.
import {login, logout} from './helpers.js';
export {login, logout};

// User를 가져온 후 바로 내보냅니다.
import User from './user.js';
export {User};
...

```
- 외부 개발자들은 `import {login} from "auth/index.js"`로 우리가 만든 패키지를 이용할 수 있습니다.
- `export ... from ...`는 위와 같이 개체를 가지고 온 후 바로 내보낼 때 쓸 수 있는 문법입니다.

```jsx
// 📁 auth/index.js
// login과 logout을 가지고 온 후 바로 내보냅니다.
export {login, logout} from './helpers.js';

// User 가져온 후 바로 내보냅니다.
export {default as User} from './user.js';
...
```

# default export 다시 내보내기

- `user.js` 내의 클래스 `User`를 다시 내보내기 한다고 가정해 봅시다.

```jsx
// 📁 user.js
export default class User {
  // ...
}
```
<문제>
1. User를 export User from './user.js'로 다시 내보내기 할 때 문법 에러가 발생합니다. 
2. export * from './user.js'를 사용해 모든 걸 한 번에 다시 내보내면 default export는 무시되고, named export만 다시 내보내집니다.

<해결방법>
```jsx
export * from './user.js'; // named export를 다시 내보내기
export {default} from './user.js'; // default export를 다시 내보내기
```
# 퀴즈 데이터 분리하기

- `App.js`에 있는 `QUISSES`상수를 외부 모듈로 분리해보도록 하겠습니다.

```jsx
// constants.js
const QUIZZES = [
    // 변수 이름을 대문자로 바꾼 이유는 변하지 않는 값은 js에서 대문자로 표현합니다.
    {
        id: 1,
        question: "일론 머스크의 우주 탐사 기업 이름은?",
        answers: [
            { text: "스페이스 엑스", isCorrect: true },
            { text: "테슬라", isCorrect: false },
            { text: "보링 컴퍼니", isCorrect: false },
            { text: "솔라시티", isCorrect: false },
        ],
    },
    {
        id: 2,
        question: "일론 머스크의 고향은 어디일까요?",
        answers: [
            { text: "미국 캘리포니아", isCorrect: false },
            { text: "남아프리카 공화국 프리토리아", isCorrect: true },
            { text: "캐나다 벤쿠버", isCorrect: false },
            { text: "호주 시드니", isCorrect: false },
        ],
    },
    {
        id: 3,
        question: "일론 머스크가 창업한 페이팔 전신 기업의 이름은?",
        answers: [
            { text: "Zip2 Corporation", isCorrect: false },
            { text: "Alpha Exploration co.", isCorrect: false },
            { text: "X.com", isCorrect: true },
            { text: "Everything CO.", isCorrect: false },
        ],
    },
];

export { QUIZZES };
```
- `constants.js`파일에 `QUIZZES` 변수를 하나 만들고 `export`문법을 통해서 밖으로 내보내 줍니다.
- `export default` 문법을 사용하지 않는 이유는 여러 변수들을 `constants.js`에서 사용할 것이기 때문에 `export`문법을 통해서 내보내 주었습니다.

```jsx
// app.js
...
import { QUIZZES } from "./constants";
// 

function App() {
    ...
    const convertedScore = Math.floor((score / QUIZZES.length) * 100);
    ...
    return (
        <div className="container">
            {showResult ? (
                <div className="app">
                    <h1 className="result-header">당신의 점수는?</h1>
                    <p className="result-score">{convertedScore}</p>
                </div>
            ) : (
                <div className="app">
                    <div className="question-section">
                        <h1 className="question-header">
                            <span>{QUIZZES[currentNo].id}</span>/{QUIZZES.length}
                        </h1>
                        <div className="question-text">{QUIZZES[currentNo].question}</div>
                    </div>
                    <div className="answer-section">
                        {QUIZZES[currentNo].answers.map((answer) => (
                            <button
                                value={answer.text}
                                onClick={() => handleClick(answer.isCorrect)}
                            >
                                {answer.text}
                            </button>
                        ))}
                    </div>
                </div>
            )}
        </div>
    );
}

export default App;
``` 

- `from "./constants";`는 실제 `constants.js`파일이 `App.js`파일과 같은 경로에 있다는 것을 의미합니다.
- `quzzise`에서 이름이 `QUIZZES`로 바뀌었기 때문에 `JSX`내에서도 변수 이름을 `QUIZZES`로 변경해 주었습니다.


## BEM(Block Element Modifier)

# 작명규칙 (Naming Convention)

- 개발,디버깅,유지보수를 위하여 CSS 선택자의 이름을 가능한 명확하게 만드는 것이 목표이다.
- 소문자 , 숫자 만을 이용해서 작명한다.
- 여러 단어의 조합은 하이픈(-)으로 연결하여 작명한다.

# 블록(Block)

- 재사용 할 수 있는 기능적으로 독립적인 페이지 구성 요소.HTML에서 블록은 class 속성으로 표시된다.
- 형태(red,big)가 아닌 목적 (menu,button)에 맞게 결정해야 한다.
- 블록은 환경에 영향을 받지 않아야 한다. 여백이나 위치를 설정하면 안된다.
- 태그,id 선택자를 사용하면 안된다.
- 블록은 서로 중첩해서 작성할 수 있다.

# 요소(Elemnet)

- 블록안에서 특정 기능을 담당하는 부분
- block_element형태로 사용
- 형태(red, big)가 아닌 목적(item, text, title)에 맞게 결정
- 요소는 중첩해서 작성
- 요소는 블록의 부분으로만 사용 할 수 있고 다른 요소의 부분으로 사용할 수 없다.
- 모든 블록에서 요소는 필수가 아닌 선택적으로 사용한다. 즉 블록안에 요소가 없을 수도 있다.

# 수식어(Modifier)

- 블록이나 요소의 모양(color, size..), 상태(disabled, checked..)를 정의한다.
- block__element — modifier, block — modifier 형태로 사용(더블 하이픈)
- 수식어의 블리언 타입과 키-벨류 타입이 있다.
- 블리언 타입 : 수식어가 있으면 값이 true 라고 가정
- 키-벨류 타입 : 키, 벨류를 하이픈으로 연결하여 표시한다. 
- 수식어는 단독으로 사용할 수 없다. 즉 기본 블록과 요소에 추가하여 사용해야 한다.

# 혼합사용(Mix)

- block1, block2__element 형태로 사용
- block2__element 에 여백이나 위치를 지정하고 block1은 독립적으로 유지

```jsx
<div class="header">
     <div class="search-form header__search-form"></div> 
</div>

```

- BEM은 클래스 명이 길게 이어지다보니 바로 css로 작성하는 것보다는 sass나 less를 이용하는 것이 효율적이다.

## Template literals

- 템플릿 리터럴은 내장된 표현식을 허용하는 문자열 리터럴입니다.
- 여러 줄로 이뤄진 문자열과 문자 보간기능을 사용할 수 있습니다.

# Syntax

```
`string text`

`string text line 1
 string text line 2`

`string text ${expression} string text`

tag `string text ${expression} string text`
```

# Description

- 이중 따옴표 나 작은 따옴표 대신 백틱(` `) (grave accent) 을 이용합니다. 
- 플레이스 홀더를 이용하여 표현식을 넣을 수 있는데, 이는 $와 중괄호( $ {expression} ) 로 표기할 수 있습니다.
- 플레이스 홀더 안에서의 표현식과 그 사이의 텍스트는 함께 함수로 전달됩니다. 
- 기본 함수는 단순히 해당 부분을 단일 문자열로 연결시켜 줍니다.
- 템플릿 리터럴 앞에 어떠한 표현식이 있다면(여기에서는 태그), 템플릿 리터럴은 "태그가 지정된 템플릿"이라고 불리게 됩니다.
- 태그 표현식 (주로 함수)이 처리된 템플릿 리터럴과 함께 호출되면, 출력하기 전에 조작할 수 있습니다.
- 템플릿 리터럴 안에서 백틱 문자를 사용하려면 백틱 앞에 백슬러쉬(\)를 넣으십시오.

```jsx
`\`` === "`" // --> true
```

# CSS-in-JS에서 CSS-in-CSS로 바꿔야 하는 이유

- Global namespace: 모든 스타일이 global에 선언되어 별도의 class 명명 규칙을 적용해야 하는 문제
- Dependencies: css간의 의존관계를 관리하기 힘든 문제
- Dead Code Elimination: 기능 추가, 변경, 삭제 과정에서 불필요한 CSS를 제거하기 어려운 문제
- Minification: 클래스 이름의 최소화 문제
- Sharing Constants: JS의 상태 값을 공유할 수 없는 문제
- Non-deterministic Resolution: CSS 로드 순서에 따라 스타일 우선 순위가 달라지는 문제
- Isolation: CSS와 JS가 분리된 탓에 상속에 따른 격리가 어려운 문제

# CSS-in-JS는 모든 문제를 해결한다?

- Global namespace: => class명이 build time에 유니크한 해시값으로 변경되기 때문에 별도의 명명 규칙이 필요하지 않다.
- Dependencies: => CSS가 컴포넌트 단위로 추상화되기 때문에 CSS 파일(모듈)간에 의존성을 신경 쓰지 않아도 된다.
- Dead Code Elimination: => 컴포넌트와 CSS가 동일한 구조로 관리되므로 수정 및 삭제가 용이하다.
- Minification: => 네임스페이스 충돌을 막기위해 BEM 같은 방법론을 사용하면 class 명이 길어질 수 있지만, CSS-in-JS는 짧은 길이의 유니크한 클래스를 자동으로 생성한다.
- Sharing Constants: => CSS 코드를 JS에 작성하므로 컴포넌트의 상태에 따른 동적 코딩이 가능하다.
- Non-deterministic Resolution: => CSS가 컴포넌트 스코프에서만 적용되기 때문에 우선순위에 따른 문제가 발생하지 않는다.
- Isolation: => CSS가 JS와 결합해 있기 때문에 상속에 관한 문제를 신경 쓰지 않아도 된다.

```jsx
const Title = styled.h1`
  color: black;
`;

const Wrapper = styled.section`
  display: flex;
  justify-content: center;
  padding: 20px;
`;

const Button = styled.button`
  background: ${(props) => (props.primary ? "black" : "white")};
  color: ${(props) => (props.primary ? "white" : "black")};
  margin: 16px;
  border: 1px solid lightgray;
  border-radius: 4px;
`;

function App() {
  return (
    <Wrapper>
      <Title>Hello World!</Title>
      <Button primary>OK</Button>
      <Button>OK</Button>
    </Wrapper>
  );
}
```

```jsx
...
<style data-styled="" data-styled-version="4.4.1">
   .fvBIug{color:black;}
   .clhVMo{display:-webkit-box;display:-webkit-flex;display:-ms-flexbox;display:flex;-webkit-box-pack:center;-webkit-justify-content:center;-ms-flex-pack:center;justify-content:center;padding:20px;}
   .iSmpeC{background:black;color:white;margin:16px;border:1px solid lightgray;border-radius:4px;}
   .fweqGX{background:white;color:black;margin:16px;border:1px solid lightgray;border-radius:4px;}
</style>
...
<div id="root">
  <section class="sc-bwzfXH clhVMo">
    <h1 class="sc-bdVaJa fvBIug">
      Hello World!
    </h1>
    <button class="sc-htpNat iSmpeC">
      OK
    </button>
    <button class="sc-htpNat fweqGX">
      OK
    </button>
  </section>
</div>
...
```

- 이렇게 되면 CSS-in-JS가 완벽한 해결책일수도 있다.
- 하지만 이렇게 좋은 CSS-in-JS를 사용했을 때 단점은 어떤것이 있을까?

# 번들 크기가 커진다

- 콘텐츠가 중요한 프로젝트일 경우 빠르게 보여주는 것이 중요한데 styled-components와 같은 별도의 라이브러리를 추가로 설치해야 하고 필요하다면 관련된 라이브러리의 추가 설치가 필요하다(회사 프로젝트에서는 styled components, styled system, rebass까지 CSS 작성을 위해 여러 라이브러리를 사용중이다). 
- CSS-in-CSS 는 별도의 라이브러리가 필요 없다. 브라우저가 CSS를 해석할 수 있기 때문에 CSS를 그대로 작성하면 된다.
- 번들 사이즈가 커진다는 의미이며 다운로드 시간이 길어지므로 초기 진입이 느려지게 된다.
- CSS-in-JS의 경우 JS가 모두 로딩된 후에 CSS 코드가 생성되기 때문에 더욱 느려질 수 밖에 없다.

- SSR(Server Side Rendering)을 적용하면 괜찮지 않냐 반문할 수 있다.
- SSR을 적용하면 초기 진입 시 완성된 HTML을 다운로드하기 때문에, 번들 파일을 내려받기 전에 콘텐츠를 볼 수 있다.
- 동적인 인터랙션이 필요한 웹사이트라면 상황은 달라진다.
- 번들이 다운되고 인터랙션에 필요한 CSS가 삽입되기 전까지 제대로 된 인터랙션이 적용되지 않을 수 있다.
- SR을 적용하려면 critical CSS를 추출해서 삽입하는 과정이 별도로 필요하다.
- 이는 번들 사이즈를 크게 하는 요인이 된다.

# 인터랙션이 늦다.

- CSS-in-JS는 인터랙티브한 웹페이지의 성능을 저하시킨다
- CSS-in-CSS는 모든 상태에 맞는 스타일을 전부 만들어두기 때문에 컴포넌트의 상태가 변경된다 하더라도 바로바로 적용이 가능하다. 
- CSS-in-JS 상태가 변경되면 우선 JS의 CSS 코드를 읽어와서 파싱하는 단계부터 시작하기 때문에 아무래도 늦어질 수 밖에 없다.
- 컴포넌트 단위로 관리되는 React와 별도로 CSS 코드를 관리해야 하기 때문에 관리 포인트가 하나 더 늘어난다.
- 인터랙션이 필요한 코드는 상태에 따라 CSS를 바꾸지 않고 global style에 미리 정의해두면 괜찮지 않을까 반문할 수 있다.
- 필요한 경우 CSS-in-JS 방식과 별도로 관리하는 것도 나쁘지 않다.
- CSS를 관리하는 포인트가 늘어나는 문제가 있다. 
- 인터랙션이 많아지면 오히려 global style이 복잡해지는 문제도 발생한다.

# sass(SCSS)와 CSS Modules를 사용하자

- 거대한 웹 서비스를 만들 때, pure CSS를 사용하는 것은 다소 무리가 있다.
- sass같은 전처리기를 사용하여 최대한 분리해서 사용할 필요가 있다.
- [css-loader]나 [sass-loader] 같은 로더를 사용하면 js 파일에서 CSS 파일을 import할 수 있다. 
- JS에서 CSS 파일을 import 한다고 해서 컴포넌트 단위로 관리가 가능한 것은 아니다. 
- 최소한 class명이 충돌하는 문제는 쉽게 해결된다.

# 결론

- 인터랙션이나 속도가 중요한 웹사이트라면 CSS-in-JS보다는 CSS-in-CSS를 방식이 바람직하다. 
- 관리자 페이지나 위키같이 정적인 웹사이트라면 CSS-in-CSS보다는 CSS-in-JS를 사용하는 것이 더 낫다. 

## 아토믹 디자인

# 리액트에서의 컴포넌트

- 컴포넌트 기반의 프로그래밍이다.
- 많은 개발자들은 이 개념을 충분하게 활용하지 못하고 DRY원칙을 위반한다.
- 리액트 공식 문서에 설계 패턴의 표준 모범 사례가 없고 대부분의 코드가 각 개발자의 개인 의견에 따라 작성이 되기 때문이다.
- 이런 개념들은 프론트엔드 프레임워크에 적용될 수 있다.
- 리액트는 프론트엔드 개발을 위해서 설계 우선 접근법을 권장한다.
- 계층 구조를 기반으로 컴포넌트들을 작성하는 것은 아토믹 디자인에서 중요한 기초 작업이다.
- 원자, 분자,유기체,템플릿,페이지의 개념은 이 계층구조를 형성하는데 도움이 된다.

# 아토믹 디자인인 이유

- 아토믹 디자인은 원자(Atoms), 분자(Molecules), 유기체(Organisms), 템플릿(Templates), 페이지(Pages)로 효과적인 인터페이스 시스템을 만든다.
- 원자는 버튼, 제목, 텍스트 입력 필드와 같은 가장 작은 구성 컴포넌트이다. 
- 원자는 모든 컴포넌트들의 기초가 되는 블록이며, 더 이상 분해 될 수 없는 필수 요소이다.
- 분자는 2개 이상의 원자로 구성되어 있으며, 하나의 단위로 함께 동작하는 UI 컴포넌트들의 단순한 그룹이다.
- HTML 텍스트 입력 필드, 레이블, 오류 메세지 또는 HTML 텍스트 입력 필드와 버튼으로 구성된 검색 컴포넌트가 있다.
- 유기체는 분자, 원자 또는 다른 유기체의 그룹으로 구성된 비교적 복잡한 그룹이다.
- 유기체들은 인터페이스의 개별적인 영역을 형성한다.
- 템플릿은 컴포넌트들을 배치하고 설계의 구조를 보여준다.
- 페이지의 실제 컴포넌트가 없을 경우, 페이지가 어떻게 보일지에 대한 골격 구조이다. 
- 페이지는 실제 컨텐츠들을 배치한 UI를 보여주며, 템플릿의 구체화된 인스턴스이다. 
 
# 장점

- 어플리케이션과 분리하여 컴포넌트를 개발하고 테스트할 수 있으며 스타일 가이드와 같은 도구에서 볼 수 있습니다.
- 통합 개발을 할 때 백엔드 어플리케이션 로직에 의존하지 않는다는 장점이 있습니다.
- 일련의 패턴이 확립되면 설계 변경이 필요한 경우에 대비하여 더 빠르고 유연성 있는 빌드 프로세스를 가질 수 있다.
- 기존의 컴포넌트들을 재사용하고 있기 때문에 디자인을 일관성 있게 통일할 수 있습니다.
- 특정 컴포넌트에 CSS가 강하게 결합되어 있기 때문에 CSS를 훨씬 잘 관리할 수 있다.
- 어플리케이션 구조에 따라 컴포넌트에서 사용되는 CSS만 렌더링하도록 해야한다.

# 단점

- 컴포넌트가 분리되어 있고 상위 컨테이너 컴포넌트들의 사이즈를 결정할 수 없을 경우 미디어쿼리를 사용하기 어렵다.
- 컴포넌트는 너비에 대해 알 수 없기 때문에 실제 페이지의 사이즈가 변경될 때 크기가 조정된다.
- 크기를 조정할 수 있는  flex, grid 와 같은 CSS 속성을 구현한 레이아웃 컴포넌트를 도입하여 해결 할 수 있다.

# 마무리

- 폴더 구조 이미지에서 어플리케이션은 단지 컴포넌트들로만 구성되어 있다.
- 컴포넌트들을 업데이트 하기 위해서는 비즈니스 로직을 가져오는 것이다.
- 뷰와 비즈니스 로직을 분리시키는 것이 좋은 방법이라고 생각한다.
- 프로젝트가 확장이 될 때 코드에서 문제를 디버깅 하기 쉽다.
- 다른 해결 방법으로는 render props 또는 리액트 hook이 있다.
- 단일 프로젝트에서 폴더의 중첩은 최대 3개에서 4개로 제한할 것을 권장한다.
-  import는 jest, webpack 또는 babel 에서 제공하는 alias 를 사용하여 깔끔하게 정리할 수도 있다.


