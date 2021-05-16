# 3주차

### Event Loop
 - Event Loop가 무엇이고 어떤 역할을 하는지 알기 위해서 먼저 javascript 엔진에 대한 이해가 필요했습니다.
 - javascript엔진은 코드를 해석하고 실행하는 프로그램 또는 interpreter입니다.
 - Chrome, nodejs환경에서 사용되는 V8 Engine, Safari에서 사용하는 Webkit등 다양한 JS 엔진이 있습니다.
 - Javascript엔진의 구조는 Memory heap, Call Stack, Web API, Callback Queue, EventLoop로 이루어져 있습니다.
 - Heap
   + Memory할당이 일어나는 부분입니다.
   + 구조화 되지 않는 넓은 메모리 영역을 지칭합니다.
 - Call Stack
   + 실행될 코드가 쌓이는 Stack 입니다.
   + LIFO 입니다.
   + 코드 실행 시 Call Stack에 코드가 최상위 Scope부터 Stack에 쌓입니다.
   + 코드 반환 시 Call Stack에서 가장 마지막으로 입력된 코드가 먼저 Out 됩니다.
 - Web API
   + 비동기 처리를 담당하는 부분입니다.
 - Callback Queue
   + Web API에서 비동기 처리가 끝난 후 실행되어야 할 내용이 쌓이는 부분입니다.
   + FIFO입니다.
   + Task Queue라고도 합니다.
 - Event Loop
   + Queue에 할당된 함수를 순서대로 Call Stack에 할당합니다.
   + Call Stack과 Callback Queue를 계속 바라보고 있으며, Call Stack이 비어있고 Callback Queue에 실행되어야 할 내용이 있다면 순서대로 Call Stack에 집어넣어 실행 될 수 있도록 합니다.
 - 예시 코드
```js
export function EventLoop(){
    setTimeout(() => {
        console.log("2s")
    }, 2000);

    setTimeout(() => {
        console.log("1s")
    }, 1000);

    setTimeout(() => {
        console.log("3s")
    }, 3000);

    setTimeout(() => {
        console.log("0s")
    }, 0);
    console.log("start");
}
```
 - 위의 코드는 1 ~ 3초를 출력하는 코드입니다.
 - 위의 코드가 실행 되는 순서는 다음과 같습니다.
  1. EventLoop()함수를 실행 시키면 가장 먼저 EventLoop가 Call Stack에 쌓입니다..
  2. 함수 내의 2초로 설정한 setTimeout함수를 만나면 Call Stack에 쌓입니다.
  3. setTimeout함수는 비동기 함수이므로 Web API로 넘어가고 해당 setTimeout함수는 return됩니다.
  4. 아래의 1초, 3초로 설정한 setTimeout도 2번, 3번과 동일하게 작동합니다.
  5. Web api에는 1s, 2s, 3s를 각각 출력시키는 console.log가 담겨져 있으며 각각 setTimeout으로 설정한 지연시간이 종료되면 Callback Queue에 쌓이게 됩니다.
  6. CallStack에는 console.log("start"), EventLoop()함수가 담겨져 있고, 마지막으로 적재된 console.log("start")가 반환되고 EventLoop()함수가 반환된다.
  7. Callback Queue에는 1s, 2s, 3s 가 순서대로 쌓여져 있는 형태입니다.
  10. 이때 Event Loop는 Call Stack과 Callback Queue를 계속 바라보고 있다가 Callback Queue에 반환해야 될 함수가 있으면서, Call Stack이 비어있을 경우, Callback Queue의 함수를 Call Stack으로 넘깁니다.
  11. Call Stack에 적재된 함수가 반환됩니다.

### 면접
 - 금주는 2번의 면접을 진행했습니다.
 1. 첫번째 면접에서는 코딩 인터뷰로 알고리즘 문제를 풀었습니다.
   + 코딩 인터뷰로 사용된 플랫폼은 코딜리티였습니다.
   + 1문제를 풀어야 했고, 약 35분간 진행했으며, 문제를 푸는데는 실패했습니다.
   + 문제 유형은 Topology sort였습니다.
   + 알고리즘 문제푸는 시간이 종료된 후 면접 진행 시 기술적인 부분에 대한 질문은 없었습니다.
   + 알고리즘 문제풀이와 Data structure를 계속해서 연습해야겠습니다.
 2. 두번째 면접에서는 코딩테스트는 없었고, 기술면접 위주로 진행하였습니다.
   + 주로 Javascript 코어에 대한 질문과, React관련 질문 이었습니다.
   + js : Scope에 대한 질문
   + js : var, let, const 키워드에 대한 질문
   + js : 일반 함수와 =>함수의 차이점
   + typescript를 사용할 수 있는지
   + react : 클래스 컴포넌트와 함수 컴포넌트의 차이점
   + react : 클래스 컴포넌트의 Life cycle을 함수 컴포넌트에서 구현하는 방법
   + react : 클래스 컴포넌트의 state와 함수 컴포넌트의 useState의 차이점
   + react : 클래스 컴포넌트와 함수 컴포넌트에서 어떤 형태의 컴포넌트를 선호하는지
   + react : Ref를 사용해보았는지에 대한 질문(개인 프로젝트 진행시 사용 경험 有)
 - 첫번째 면접은 알고리즘 문제 자체를 이해하는데 시간이 오래 걸린점, 처음으로 보는 알고리즘 코딩인터뷰라 긴장이 많이 되었고 주로 준비한 기술적인 부분에 대한 질문을 하지 않아서 아쉬웠습니다.
 - 두번째 면접은 질문 내용이 평소에 이해를 하고 있었고, 준비하고 있던 부분에 대한 질문이 많았으므로 다행히 답변을 많이 했지만 아직 부족함을 많이 느꼈습니다.