# React v17

2020년 10월 20일 React v17이 릴리즈 되었다.
리엑트 이전 주요 릴리즈 이후 2년 반만에 릴리즈되는 것으로 많은 관심들이 있었는데 과연 어떤 내용들이 추가되었는지 살펴보자.

## No New Features
 처음에 밝히듯히 새롭게 추가되는 기능들은 없다.
이번 릴리즈는 **리엑트를 더 쉽게 upgrade** 가능하도록 하는데 초점이 맞춰졌다.

## Gradual Upgrades
15, 16 버전의 React를 사용하고 있을 때 17 버전으로 업그레이드를 위해서는 전체 app을 업그레이드 했어야했다.
만약 예전에 작성되었고, 유지보수가 제대로 이루어지지 않은 코드일 경우 이러한 업그레이드는 힘들어질 수 있으며 이럴때 두가지 버전의 React를 사용할 수 있지만 Event 문제가 발생했다.
React 17에서 이러한 문제점들을 수정하였으며 React 18과 같은 다음 버전이 나왔을 때 업그레이드 시 더 많은 옵션을 사용할 수 있게 되었다.

이전처럼 전부 한꺼번에 업그레이드를 하거나, 하위 라우터 몇 개는 이전 버전을 사용하게 둘 수도 있게 되었다. 이를 Gradual Upgrades라고 부른다.
하지만 Gradual Upgrades는 추천되지않으며 앞에 설명한 앱 전부를 업그레이드하는 것을 추천한다.

## Changes to Event Delegation
앞의 Gradual Upgrades를 설명하면서 두가지 버전의 React를 사용할 때 Event 문제가 발생할 수도 있다고 하였다.
React 17에서는 이를 해결하기 위해서(두가지 React버전을 사용할 수 있게 하기위해) React event system의 변경이 필요하였다.

React 17에서는 event handler를 `docuemnt`에 달지않고 React tree가 랜더되는 root DOM container에 달았다.
```jsx
const rootNode = document.getElementById('root');
ReactDOM.render(<App/>, rootNode);
```
이전 버전들에서는 `document.addEventListener()`를 사용했다면 React 17부터는 `rootNode.addEventListener()`를 사용하게 된다.

## Other Breaking Changes
이외에 여러 자잘자잘한 버그들이 수정되었는데
한 가지 눈에 띄는 것이 있어 소개하려고한다. (실제 프로젝트를 진행하다 발견한 문제인데 이번 v17에 추가되어 기뻤다.)

- Add the `disableRemotePlayback` property for `video` elements. (@tombrowndev in [#18619](https://github.com/facebook/react/pull/18619))
  
`video` Tag에 `disableRemotePlayback` 속성이 추가된 것인데 [Issue]((https://github.com/facebook/react/issues/18618))를 살펴보면 자세히 알 수 있다.

웹페이지에 `video` Tag를 이용해 동영상을 넣고 안드로이드 휴대폰으로 접속하면 동영상 위에 `chromecast` icon이 보여지게 된다. https://stackoverflow.com/questions/28153166/android-chrome-chromecast-icon-over-html5-video-position

이것은 [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/disableRemotePlayback)에 나오듯 `disableRemotePlayback`을 true로 두면 없앨 수 있는데 React에서 지원하지 않았다가 이번 v17에서 사용할 수 있게 된 것이다.


## Reference

- [React v17.0](https://reactjs.org/blog/2020/10/20/react-v17.html)
