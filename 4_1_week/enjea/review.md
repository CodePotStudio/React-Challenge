# 챌린지 1주차 리뷰

## DOM
돔(DOM)이란 Document Object Model의 약자이며, HTML 및 XML 문서를 위한 API이다. 돔은 객체 간의 관계를 지정하지 않았기 때문에 논리적 구조라고 하며, 개발자는 DOM을 사용하여 문서를 작성하고 구조를 탐색하며, 돔에 요소 또는 내용을 추가, 수정, 삭제할 수 있다.

## Virtual DOM
### Virtual DOM이 생겨난 이유
- Element를 추가, 수정, 생성 하면서 DOM이 수정되면 Render Tree 생성, Layout 생성, Paint가 다시 이루어진다. 이는 리소스가 변경 될 때마다 젼체 페이지를 갱신 시키는 것인데, 변하지 않는 요소까지 모두 바꾸어버려서 렌더링이 비효율적인 문제가 발생.
- SPA(Single Page Application)특징으로 DOM 복잡도 증가에 따른 최적화 및 유지 보수가 더 어려워짐.

### Virtual DOM 방식
- Virtual DOM은 DOM을 추상화한 가상의 객체를 메모리에 만들어놓은 것이다.
- 브라우저에 Render를 요청 하기 전에 변경사항을 요청 하는 방식이다.
-  변경사항을 DOM에 직접 수정하는 것이 아니라 중간 단계로 Virtual DOM을 수정하고 Virtual DOM을 통해서 DOM을 수정하게 했다. 
- Virtual DOM에 변경 내역을 한번에 모으고(버퍼링) 실제 DOM과 변경된 Virtual DOM의 차이를 판단한 후, 구성 요소의 변경이된 부분만 찾아 변경하고 그에 따른 렌더링은 한번만 하는 것으로 해결되었다.