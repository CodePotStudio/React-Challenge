# Vanilla JS

>  React를 하기에 앞서 오래도록 사용하지 않아 잊어버렸던 개념을 다시 공부하기 위해 javascript 재입문 단계를 밟는다.

순수하게 브라우저 위에서 돌아가는 javascript



## 데이터 타입

- 수 (Number)
- 문자열 (String)
- 불(Boolean)
- 객체 (Object)
  - 함수(Function)
  - 배열(Array)
  - 날짜(Date)
  - 정규식(RegExp)
- 기호 (Symbol) -ES2015에 새로 추가됨
- 널(Null)
- 정의되지 않음(Undefined)



### Number

Javascript는 `BigInt`를 제외하고 **정수와 같은 것이 존재하지 않는다.** 또한 정수는 32비트 정수로 처리된다.

```javascript
0.1 + 0.2 = 0.30000000000000004
```

산술 연산자를 지원하며, 고급 수학 함수를 다루는 `Math` 객체가 존재한다.

내장 `parseInt` 를 사용해서 문자열을 정수로 변환한다.

```javascript
parseInt('123', 10); // 123
parseInt('11', 2); // 3 => 2진수 11을 10진수의 정수로 변경
```

`NaN(숫자가 아님을 뜻함)` 개념을 다양하게 사용할 수 있다.

```javascript
parseInt('hello', 10); // NaN
NaN + 5; // NaN
isNaN(NaN); // true
```



### String

js의 문자열은 유니코드 문자로 만들어졌다. js에서는 String도 객체로 취급된다.



## 변수

- let : 지역 변수 선언, 업데이트 가능
- const : 상수, 변하지 않는 값
- var : 전역 변수, 업데이트 가능



## 연산자

서로 다른 타입의 변수에 연산자를 이용할  수 있다.

```javascript
'3' + 4 + 5 // '345'
3 + 4 + '5' // '75'
```

삼항 연산자를 사용할 수 있다.

```javascript
let a = 1
let b = (a > 0) ? 'yes' : 'no';
```



## 반복문

총 3가지의 반복문을 갖는다.

- `for (var i=0, i<100; i++){}`
- `for (let i of array)` : Value로 작업을 실행한다.
- `for (let i in array)` : object의 항목(property)로 작업을 실행한다.



## 객체

python의 dictionary와 같은 역할을 한다.

```javascript
let a = new Object();
let b = {};
```



## 함수

`arguments` 를 활용하면 원하는 만큼 매개변수를 받을 수 있다.

```javascript
function add() {
  var sum = 0;
  for (var i; j = arguments.length; i<j; i++){
    sum += arguments[i];
  }
  return sum;
}
add(2,4,5,6);
```

`Rest 파라미터 문법` 을 이용하면 보다 간결하게 작성 할 수 있다.

```javascript
function add(...args) {
  var sum = 0;
  for (let value or args) {
    sum+=value;
  }
  return sum
}
add(2,3,4,5)
```

## 사용자 정의 객체

js는 프로토타입 기반 언어라서 클래스는 없지만, 클래스처럼 코딩을 할 수는 있다.

```javascript
function Person(first, last){
	this.first = first;
	this.last = last;
  	this.fullName = function() {
      return this.first + ' ' + this.last;
  	}
}
var minsoo = new Person('minsoo', 'han')
```

위의 방법은 새로운 `Person` 객체를 사용할 때마다 fullName 함수를 만들기 때문에 효율적이지 않다. 프로토타입 속성으로 이를 해결한다.

```javascript
function Person(first, last) {
  this.first = first;
  this.last = last;
}

// prototype은 Person 객체끼리 서로 공유되는 객체다.
Person.prototype.fullName = function(){
  return this.first + ' ' + this.last;
}
```

실제 동작은 prototype 문법으로 작동하지만 (ES6 이후) class 문법을 사용하여 마치 클래스인 것처럼 사용할 수 있다.

```javascript
class Person {
  constructor(first, last) {
    this.first = first;
    this.last = last;
  }
  fullname () {
    return this.first + ' ' + this.last;
  }
}
```



## 내장 함수

다른 함수의 내부에서 또 다른 함수를 선언하는 경우 내장 함수라고 한다.

```javascript
function Afunc () {
  var a = 1000000;
  
  function Bfunc() {
    var b = 2000000;
    return a + b;
  }
  return Bfunc();
}
```



## 클로저

js의 추상 개념이다.

```javascript
function makeAdder(a){
  return function(b){
    return a+b;
  }
}

var add5 = makeAdder(5);
var add20 = makeAdder(20);
add5(6); // 11
add20(7); // 27
```

위의 `var add5` 에서 바깥 함수인 `makeAdder` 가 또 다른 함수를 리턴했기 때문에 다시 한번 함수 호출을 통해 결과값을 얻게된다.

**공부 링크** : https://ko.reactjs.org/docs/hello-world.html