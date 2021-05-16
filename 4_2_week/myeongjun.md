# 2주차

## 브라우저와 DOM, Virtual DOM
 - React는 Virtual DOM을 사용합니다.
 - Virtual DOM을 이해하기 위해 웹 브라우저, 브라우저의 작동 방식, DOM에 대한 이해가 필요합니다.

 ### 웹 브라우저 란?
 - 브라우저는 사용자가 인터넷에 접속하기 위해 사용하는 S/W입니다.
 - 대표적으로 Chrome, Safari, Edge등이 있습니다.
 - 우리는 브라우저를 통하여 웹에 접속하고 각종 편리 서비스를 제공받을 수 있습니다.
 - 브라우저는 HTML파일과 CSS명세에 따라 HTML 파일을 해석하여 표시하는데 이 명세는 웹 표준화 기구인 W3C(World Wide Web Consortium)에서 정합니다.
 - 브라우저의 역할은 HTML, CSS, JS/TS, WASM과 같은 언어를 해석/렌더링하여 화면에 출력시켜 줍니다.

### 브라우저의 구조
 1. UI(User Interface)
  - 주소 표시줄
  - 이전페이지로 돌아가기 / 다음페이지로 이동하기
  - 북마크
  - 새로고침
  - 로딩중지
  - 홈으로 이동하기
 2. Browser engine
  - UI와 렌더링 엔진 사이의 동작을 제어합니다.
  - 사용자가 UI의 특정 버튼을 눌렀을 경우 이를 수행하도록 합니다.
 3. Rendering engine
  - 요청된 콘텐츠를 화면에 표시
  - 대표적인 Rendering engine으로 Blink, Gecko, Webkit 등이 있습니다.
 4. Networking
  - HTTP와 같은 Network호출에 사용됩니다.
  - Server에 요청/응답을 받습니다.
 5. UI Backend
  - OS별 Interface에 따르는 UI를 처리합니다.
  - 예를 들면 콤보박스의 디자인 형태가 window, mac, linux 등의 OS별로 각각 차이가 있고 이에 따른 UI를 처리합니다.
 6. Javascript Interpreter
  - JS코드를 해석하고 실행합니다.
  - Chrome에 탑재된 V8엔진이 있습니다.
 7. Data Persistence
  - 데이터를 저장하는 계층입니다.
  - Cookie나 Local Storage 등이 있습니다.

### 브라우저 렌더링 과정
 - 브라우저를 렌더링 하기 위해 렌더링 엔진을 사용합니다.
 - 렌더링 엔진의 동작 순서는 크게 4가지로 구성할 수 있습니다.
 - Parsing, Render Tree, Layout / Reflow, Painting
 
 1. Parsing
  + Parsing이란 토큰화된 코드를 구조화 하는 과정입니다.
  + 토큰화란 의미있는 최소단위로 코드를 나누는 과정을 의미합니다.
  + Parsing을 통해 HTML파일로 DOM트리를 구축하고, CSS파일로 CSSOM트리를 구축합니다.
  + 브라우저가 HTML을 전달받으면, Rendering engine이 이를 Parsing하여 DOM노드로 이루어진 트리를 구축합니다.
  + 토큰화된 HTML의 문자열을 이용하여  Parse Tree를 생성합니다.
  + HTML을 Parsing하여 Parser Tree를 생성하고, 이를 활용하여 DOM Tree를 구축합니다.
  + CSS Parser는 CSS파일 모두를 다운받은 후 Parsing을 실행할 수 있으며, CSS Parsing이 모두 종료되면 CSSOM(CSS Object Model)트리를 구성합니다.(스타일, 규칙, 선택자 등의 정보가 node에 입력됩니다.)
  + Parsing을 통해 작성된 언어가 Syntax에 맞게 작성이 되어있는지 검사합니다.
  + Parsing을 전문적으로 해주는 것을 Parser라고 합니다.

 2. Render Tree
  + DOM및 CSSOM을 결합하여 Render Tree를 구축합니다.
  + 화면에 나타나는 요소를 결정하는 트리입니다.
  + 요소의 Style이 계산됩니다.

 3. Layout / Reflow
  + 각 노드의 좌표가 주어지고 화면의 어디에 나타나야 할 지 위치가 계산됩니다.
  + Root Object부터 재귀적인 알고리즘으로 실행 됩니다.
  + 노드의 크기와 위치가 처음 결정되는 시기를 Layout이라고 하며, 이를 후속 재계산 한다면 Reflow라고 합니다.
  + 화면 전체의 Layout을 계산하는 *전역적 레이아웃*, 특정 요소의 Layout만 계산하는 *중분적 레이아웃*이 있습니다.
 
 4. Painting
  + Layout / Reflow단계에서 화면에 배치된 Element에 색을 입히고 Layer의 위치를 결정하는 단계입니다.
  + Root Object로 부터 재귀적으로 실행이 됩니다.
  + 트리의 각 Node를 거치며 paint() 메소드를 호출합니다.
  + Layout단계와 마찬가지로 전역적 페인팅과 중분적 페인팅이 있습니다.

### DOM, Document Object Modle
 - DOM은 HTML, XML과 같은 문서의 프로그래밍 Interface입니다.
 - 문서의 구조화 된 표현을 제공하며, 프로그래밍 언어가 DOM 구조에 접근하여 문서 구조를 동적으로 변경할 수 있도록 돕습니다.
 - Node와 property, method를 가지고 있는 Object문서로 표현하며, 이들은 Web page를 script 또는 프로그래밍 언어들 에서 사용할 수 있도록 연결시켜주는 역할을 담당합니다.

### Virtual DOM
 - DOM에 변화가 생기면 위에서 설명한 Rendering과정을 반복합니다.
 - 복잡한 SPA(Single Page Application)에서는 DOM 조작이 많이 발생하는데, 그때마다 Rendering과정을 거치게 되는데, 이는 굉장히 비효율적입니다.
 - Virtual DOM은 이러한 비효율적인 프로세스를 완화하는데 도움을 줍니다.
 - 만약 DOM에 변화가 생긴다면 그 내용을 Real DOM에 적용되기 전에 Virtual DOM에 먼저 적용한 후 최종 결과를 Real DOM에 전달해줌으로써, 브라우저에서 발생하는 불필요한 연산의 양을 줄이는데 도움을 줄 수 있다.
 - DOM fragment를 관리하는 과정을 자동화 해주어 생산성을 향상시켜줍니다.

* 참고
<d2.naver.com/helloworld/59361>
<gs.statcounter.com>
<wormwlrm.github.io/2021/03/27/How-browsers-work.html>
<velopert.com/3236>
<developer.mozilla.org/ko/docs/Web/Performance/How_browsers_work>
<developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction>

## 데이터 바인딩
 - 데이터 바인딩이란 두개 이상의 데이터 또는 정보의 소스를 모두 merge하는 기법입니다.
 - Web Application에서는 HTML정보를 담고있는 템플릿과, 컴포넌트가 가지고 있는 동적 데이터를 merge해야 하고, 이를 가능하도록 하는 것이 데이터 바인딩입니다.
 - 데이터 바인딩은 양방향 바인딩과 단방향 바인딩이 있습니다.
 - React라이브러리는 단방향 바인딩을 사용합니다.
 - 양방향 바인딩은 html에서 javascript코드의 데이터를 변경할 수 있고, javascript코드에서 html의 데이터를 변경할 수 있습니다.
 - 단방향 바인딩은 javascript코드에서 html의 데이터를 변경 가능하고, html에서 javascript코드의 데이터를 변경하는 방법을 제공하지 않습니다.