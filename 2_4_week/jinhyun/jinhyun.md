### 스타일링 방식
 - 일반 css : 컴포넌트를 스타일링하는 가장 기본적인 방식
 - Sass : 자주 사용되는 CSS 전처리기 중 하나로 확장된 CSS 문법을 사용하여 CSS 코드를 더욱 십게 작성할 수 있게 도와줍니다.
 - CSS Module : 스타일을 작성할 때 css 클래스가 다른 css 클래스의 이름과 절대 충돌하지 않도록 파일마다 고유한 이름을 자동으로 생성해주는 옵션입니다.
 - styled-components : 스타일을 자바스크립트 파일에 내장시키는 방식으로 스타일을 작성함과 동시에 해당 스타일이 적용된 컴포넌트를 만들 수 있게 해줍니다.
  
## 3.1. 일반 css
- 가장 흔한 방식
   
```css
.App{
  text-align: center;
}
.App .logo{
  height: 40px;
}
```

## 3.2. Sass
- Syntactically Awesome Style Sheets (문법적으로 매우 멋진 스타일 시트)의 약자입니다.
- CSS 전처리기로 복잡한 작업을 쉽게 할 수 있도록 해주고, 스타일 코드의 재활용성을 높여 줄 뿐만 아니라 코드의 가독성을 높여서 유지 보수를 더욱 쉽게 해줍니다.
- 두 가지 확장자 .scss, .sass를 지원합니다.
- 변수의 사용
- 조건문과 반복문
- Import
- Nesting
- Mixin
- Extend/Inheritance
  
```css
 // sass
.list
  width: 100px
  float: left
  li
    color: red
    background: url("./image.jpg")
    &:last-child
      margin-right: -10px
```

```css
// scss
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
     -moz-border-radius: $radius;
      -ms-border-radius: $radius;
          border-radius: $radius;
}

.box { @include border-radius(10px); }
```

- .sass 확장자는 중괄호({})와 세미콜론(;)을 사용하지 않습니다. 반면 .scss 확장자는 기존 css를 작성하는 방식과 비교해서 문법이 크게 다르지 않습니다.
- Sass의 장점 중 하나는 라이브러리를 쉽게 불러와서 사용할 수 없다는 점입니다.


## 3.3. CSS Module

```css
// Box.module.css
.Box {
  background: black;
  color:white;
  padding: 2rem;
}

```

- 리액트 컴포넌트 파일에서 해당 css 파일을 불러올 때 css 파일에 선언한 클래스 이름들이 모두 고유해집니다. 고유 css 클래스 이름이 만들어지는 과정에는 파일 경로, 파일 이름, 클래스 이름, 해쉬값 등이 사용 될 수 있습니다.

- 클래스 이름에 대하여 고유한 이름들이 만들어지기 때문에, 실수로 CSS 클래스 이름이 다른 관계 없는 곳에서 사용한 CSS 클래스 이름과 중복되는 일에 대하여 걱정 할 필요가 없습니다.

- 이 기술은 다음과 같은 상황에 사용하면 유용합니다.
  - 레거시 프로젝트에 리액트를 도입할 때 (기존 프로젝트에 있던 CSS 클래스와 이름이 중복되어도 스타일이 꼬이지 않게 해줍니다.)
  - CSS 클래스를 중복되지 않게 작성하기 위하여 CSS 클래스 네이밍 규칙을 만들기 귀찮을 때 
  - 만약 특정 클래스가 웹 페이지에서 전역적으로 사용되는 경우라면 :global을 앞에 입력하여 글로벌 css임을 명시해줄 수 있습니다.


## 3.4. styled-components
- 자바스크립트 파일 안에 스타일을 선언하는 방식, CSS-in-JS라고 합니다.
- emotion이 대표적으로 사용될 수 있습니다.
- css 또는 scss 확장자를 가진 스타일 파일을 따로 만들지 않아도 된다는 장점이 있습니다.
- props 값으로 전달해주는 값을 쉽게 스타일에 적용할 수 있다는 것입니다.
