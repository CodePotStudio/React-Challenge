# 3주차 학습

## super란?

Hook 이전의 클래스형 리액트 컴포넌트 코드들을 살펴보다보면 다음과 같은 코드들이 많이 있습니다.

```javascript
class Checkbox extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOn: true };
  }
}
```



`super` 란 무엇일까요?

<br>

---

`super` 키워드는 부모 객체의 함수나 속성에 접근하는데 사용됩니다.

기초부터 살펴봅니다.

상위 속성에 접근한다는 것은 무슨 의미일까요?

```javascript
class A { }
A.prototype.x = 100;

class B extedns A {
  m() {
    return super.x;
  }
}

const b = new B();
b.m();
```

A는 B의 상위 클래스이고, `b.m()` 은 100을 리턴합니다.

![pic1](./pic1.svg)

사실, 자바스크립트의 프로토타입 상속은 이 그림보다 훨씬 복잡합니다.

![pic2](./pic2.svg)

우선 `__proto__ (던더프로토)` 와 `prototype` 속성이 다르다는 것을 알아야합니다.

`B.prototype` 은 `new B()` 로 생성된 인스턴스 객체의 `__proto__` 가 될 객체이다.

따라서 `b.__proto__ === B.prototype` 이다.

같은 매커니즘으로 `B.prototype` 은 자신만의 `__proto__` 속성을 가지게 되는데, 이 속성은 `A.prototype` 과 동일하다.

이것이 바로 프로토타입 체인이다.

```javascript
b ->
  b.__proto__ === B.prototype ->
  B.prototype.__proto__ === A.prototype ->
  A.prototype.__proto__ === Object.prototype ->
  Object.prototype.__proto__ === null
```

프로토타입 체인을 통해,`new B()` 로 생성된 인스턴스 객체  `b` 는 모든 정의된 속성에 접근 가능하다.

아까전 `b.m()` 어떻게 `100` 을 리턴했는지 살펴보자.

`b` 에는 `m` 메소드가 없으므로, 프로토타입 체인을 통해 `b.__proto__ === B.prototype` 을 찾아본다.

`m` 메소드는 `B.prototype` 의 속성이므로, `B.prototype.m` 이 실행되는 것이다.

`m` 메소드 내부의 `super.x` 는 속성 조회(lookup)이라는 것을 알 수 있다.

`x` 를 찾을 때까지 홈 객체의 `__proto__` 와 프로토타입 체인을 거슬러 올라간다.

> 홈 객체란, 메소드가 정의된 객체를 의미한다.
>
> 지금의 경우, `m` 의 홈 객체는 `B.prototype` 이다.

홈 객체의 `__proto__` 는 `A.prototype` 이고, `A.prototype` 에서 `x` 를 찾게된다.

---

## Reference

https://v8.dev/blog/fast-super

https://min9nim.github.io/2018/12/super-props/



---



## Generator

> 아직 리덕스를 사용해본 적이 없는데요.
> redux, redux-thunk, redux-saga 등 비슷한 이름이 많더라구요.
> 이게 다 뭘까? 싶어서 보다가.. 
> 리덕스 사가에서 제너레이터를 사용하는데 어떻게 사용하는지 몰라서 학습을 하게 되었습니다.

제너레이터를 사용하면 함수를 특정 구간에서 멈춰놓을 수 있고, 또 다시 돌아가게 할 수도 있습니다.

```javascript
function weirdFunction() {
  return 1;
  return 2;
  return 3;
}
```

여러번에 걸쳐서 값을 반환하는 말도 안되는 함수이지만, 제너레이터 함수는 이런식으로 값을 순차적으로 반환할 수 있습니다.

제너레이터 함수를 만들어보겠습니다.

```javascript
function* generatorFunc() {
  console.log('제너레이터 00');
  yield 1;
  console.log('제너레이터 01');
  yield 2;
  console.log('제너레이터 02');
  yield 3;
  
  return 4;
}
```

`제너레이터 함수` 를 호출하면, 객체가 반환되는데요. 이 객체를 `제너레이터` 라고 합니다.

```javascript
const generator = generatorFunc();
```

제너레이터 객체에는 `next` 메소드가 있는데 이 메소드를 호출해야 코드가 실행되고, `yield` 를 한 값을 반환하고 코드의 흐름이 멈추게 됩니다.

```javascript
generator.next();
// 제너레이터 00
// {value: 1, done: false}

generator.next();
// 제너레이터 01
// {value: 2, done: false}

generator.next();
// 제너레이터 02
// {value:3, done: false}

generator.next();
// {value: 4, done: true}

generator.next();
// {value: undefined, done: true}
```



제너레이터 함수에 인자를 넘겨서 함수 내부에서 사용할 수도 있는데요.

```javascript
function* sumGenerator() {
  console.log('sum generator 시작');
  let a = yield;
  console.log('a값 받았음.');
  
  let b = yield;
  console.log('b값 받았음.');
  
  yield a + b;
}

const sum = sumGenerator();

sum.next();	
// sum generator 시작
// {value: undefined, done: false}

sum.next(5);
// a값 받았음.
// {value: undefined, done: false}

sum.next(10);
// b값 받았음.
// {value: 15, done: false}

sum.next();
// {value: undefined, done: true}
```



### Generator로 액션 모니터링하기

원리를 살펴보면, 타입을 미리 정해놓고 해당 타입에 해당하는 액션을 실행하는 것 같습니다.

```javascript
function* watchGenerator() {
  console.log('모니터링 시작');
  
  while(true) {
    const action = yield;
    if(action.type === 'commit') {
      console.log('커밋');
    } 
    if(action.type === 'dispatch') {
      console.log('디스패치')
    }
  }
}

const watch = watchGenerator();
watch.next();
// 모니터링 시작
// {value: undefined, done: false}

watch.next({type: 'commit'});
// 커밋
// {value: undefined, done: false}

watch.next({type: 'dispatch'});
// 디스패치
// {value: undefined, done: false}
```





#### 레퍼런스

https://react.vlpt.us/redux-middleware/10-redux-saga.html