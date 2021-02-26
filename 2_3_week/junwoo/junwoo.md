# 3주차 학습

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



 



## Reference

https://v8.dev/blog/fast-super

https://min9nim.github.io/2018/12/super-props/