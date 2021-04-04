---
title: Efficiently load third-party JavaScript (1)
author: mingyu.gu
date: 2021.04.04
---

# Efficiently load third-party JavaScript (1)

> Web.dev의 [Efficiently load third-party JavaScript](https://web.dev/efficiently-load-third-party-javascript/)를 참고하여 작성하였습니다.

Third-party script 가 페이지의 로드를 느리게 할 경우, 성능을 향상시키기 위한 두가지 옵션이 있습니다.

- 사이트에 명백한 가치를 주지않다면 제거해라.
- loading process를 최적화 시켜라

loading progess를 최적화시키는 방법으로는 다음과 같은 방법들이 있습니다.

1. `<script>` 태그에 `async` 또는 `defer` attribute를 사용
2. 필요한 origins에 미리 connections을 시켜놓기
3. Lazy-loading
4. third-party scripts serving을 최적화

이번 포스팅에는 1번 방법만 설명하도록 하겠습니다.

## Use async or defer

Synchronous scripts는 DOM Pharsing을 막아 rendering을 지연시키기에, third-party scripts의 로드는 항상 비동기적으로 실행시켜야하며 page가 render되기 전에 script를 실행시킬 필요가 없습니다.

`async`와 `defer` 속성은 브라우저에게 HTML이 parsing되는 중에 background에서 script를 로드하고 load가 된 후에 script를 실행시킨다고 말해줍니다. 이러한 방법으로 script download는 DOM 생성과 rendering을 block하지 않습니다. 이러한 방법으로 user는 모든 스크립트가 로드되기 전에 page를 볼 수 있습니다.

```html
<script async src="script.js">

<script defer src="script.js">
```

`async`와 `defer`의 차이점은 scripts의 실행 시점에 차이가 있습니다.

### async

`async` 속성을 가진 Sscript는 download를 마친 후 window's의 load event 전에 실행시킵니다. 이는 비동기 스크립트가 HTML에 나타나는 순서대로 실행되지 않을 가능성이 있다는 것을 의미합니다. 또한 DOM tree 생성을 하고 있는 도중 script 다운로드가 완료되면 DOM tree 생성을 interrupt 할 수도 있습니다.
![](https://web-dev.imgix.net/image/admin/tCqsJ3E7m4lpKOprXu5B.png?w=1600)

### defer

`defer` 속성을 가진 scripts는 HTML parsing이 완료된 이후, DOMContentLoaded 이벤트 이전에 실행됩니다. `defer`는 HTML이 순서대로 실행되는 것을 보장하고 parsing을 block하지 않습니다.
![](https://web-dev.imgix.net/image/admin/Eq0mcvDALKibHe15HspN.png?w=1600)

- `async`는 load process 초기에 스크립트를 실행시키는 것이 중요할 때 사용합니다.
- `defer`는 덜 중요한 요소에 사용하는 것이 좋습니다. 예를 들어 스크롤을 해야 볼 수 있는 동영상 플레이어.

### Code

실제 코드에서 이론대로 동작하는지 확인해보겠습니다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <script defer src="./defer.js"></script>
    <script async src="./async.js"></script>
    <script src="./sample.js"></script>
    <script>
      console.log("in head");
      window.addEventListener("DOMContentLoaded", function () {
        console.log("DOMContentLoaded is loaded");
      });

      window.onload = function () {
        console.log("window is loaded");
      };
    </script>
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <script>
      console.log("end of body");
    </script>
  </body>
</html>
```

다음과 같은 코드가 있다고 했을 때 콘솔에는 다음과 같이 찍혔고 생각했던 대로 동작함을 확인할 수 있었습니다.

```
in sample
in head
end of body
defer
DOMContentLoaded is loaded
async
window is loaded
```

## 마무리

실제 프로젝트를 하면서 third-party javascript library를 사용하는 경우가 많습니다. kakao-sdk, google-analytics, google tag manager, facebook-sdk, etc... 이러한 라이브러리를을 아무렇게나 사용하게 되면 First Paint가 늦어져 사용자 입장에서는 페이지 로딩이 느린, 안좋은 경험을 할 수 있습니다. 이러한 문제들은 script 태그에 `async`, `defer` 속성을 적절히 사용함으로써 해결할 수 있습니다.
