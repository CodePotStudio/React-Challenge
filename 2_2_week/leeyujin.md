## 모던 Javascript 빠르게 훓어보기

# 화살표 함수

- 화살표 함수는 일반 함수와 기능은 같으면서 코드를 좀 더 간결하게 쓸 수 있도록 도와줍니다.

```jsx
// sum 함수 만들기 with 일반 함수
function sum (a,b) {
    return a + b;
}

// function 사용하기 
sum (1,2);
```
- `sum`함수는 `a`와 `b`를 변수로 받고 두 값을 합쳐서 외부로 반환해주는 역할을 합니다.

```jsx
// sum 함수 만들기 with 화살표 함수
const sum = (a,b) => {
    return a + b;
};

// function 사용하기
sum (1,2);
```
- `function`명령어가 화살표(=>)로 바뀌었고 `const` 명령어를 통해서 `sum`변수에 함수를 담아주었습니다.
- 함수 내에서 바로 return하는 경우 추가적으로 축약해줄 수 있습니다.

```jsx
// 바로 return하는 경우 return 생략하기
const sum = (a,b) => a +b;
```

# 한 단계 더 코드를 간결하게 만들기

```jsx
// 숫자를 받아서 제곱을 하는 함수를 만들기
const square = n => n * n;
```
```jsx
// 매개변수가 하나 일 떄는 소괄호도 뺄 수 있습니다.
const square = (n) => n * n;
square(10); 
```
- 화살표함수를 사용하면 return도 축약 가능하다. { return }

## 구조 분해 할당

- 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 JavaSript 표현식입니다.

# 객체 구조 분해 할당

```jsx
const people = {
    name : "leeyujin",
    address : "Incheon",
};

const name = people.name; // people['name']
const address = people.address; // people['address']
console.log(name,address);
// leeyujin Incheon
```
- 일반적으로 객체 내에 있는 변수 값들을 외부에서 꺼낼려고 한다면 `.` 혹은 `[]`로 프로퍼티의 이름으로 접근해서 가져옵니다.
- 하지만 이 과정에서 people을 중복적으로 쓰게 되고 프로퍼티가 많아질 수록 코드의 중복이 많아집니다.

```jsx
const people = {
    name : "leeyujin",
    address : "Incheon",
};

const {name, address} = people
console.log(name,address);
// leeyujin Incheon
```
- 두 줄의 코드를 한줄로 바꾸니 코드가 간결해진 것을 알 수 있습니다.

```jsx
const people = {
    name : "leeyujin",
    address : "Incheon",
    detailAddress: "Namdong",
};

const { name, address, detailAddress } = people;
console.log(name, address, detailAddress);
// leeyujin Incheon Namdong
```
- 프로퍼티를 추가하고자 한다면 중괄호 안에 객체의 추가 된 프로퍼티를 넣어주면 됩니다.
- `반드시 객체 내에 있는 프로퍼티 명을 사용`해 주어야 합니다. (에러 발생)

```jsx
// 정의된 이름이 마음에 안들어 변경하고 싶을 때

const people = {
    name : "leeyujin",
    address : "Incheon",
    detailAddress: "Namdong",
};

// const peopleName = people.name;
const { name : peopleName } = people;
console.log(peopleName);
// leeyujin
```
- `:새로운 변수명`처럼 중괄호 안에 변경하고 싶은 프로퍼티 옆에 `:`과 함께 새로운 변수명을 넣어주면 됩니다.

# 배열 비구조 할당

```jsx
const fruits = ["사과","감"];
// const firstFruit = Fruits[0];
// const secondFruit = Fruits[1];

const [firstFruit,secondFruit] = fruits;
console.log(firstFruit,secondFruit);
```
- 비구조 할당은 배열에서도 사용할 수 있습니다.
- 객체 비구조 할당과 다른점은 `{}`가 아닌 `[]`를 활용한다는 점과 배열은 객체와 달리 프로퍼티의 이름이 없습니다.
- 배열에 순서에 맞추면 변수에 값이 할당됩니다.

## 객체에 대한 설명

- 자바스크립트에는 총 8가지 자료형이 있는데 이 중에서 **7개의 경우 하나의 데이터 (문자열,숫자 등)만 담을 수 있기 때문에 '원시형(primitive type)'** 이라 부릅니다.
- 객체형은 원시형과 달리 **다양한 데이터를 담을 수 있으며 키로 구분되어 있는 데이터 집합이나 복잡한 개체를 저장**할 수 있습니다.
- 객체는 {...}를 이용해서 만들 수 있습니다. 이 안에는 '키(key):값(value)' 그리고 쌍으로 구성된 프로퍼티(property)를 여러게 넣을 수 있습니다.
- **키에는 문자형** / **값에는 모든 자료형**이 허용되며 프로퍼티 키는 프로퍼티 이름으로 부릅니다.



- **서랍장 안 파일은 프로퍼티**이며 파일 각각에 붙어있는 **이름표는 객체의 키**라고 생각하면 된다. (그러므로 객체에서는 키를 이용해서 프로퍼티를 찾을 수 있다. 이는 추가나 삭제도 마찬가지이다.)

```jsx
let user = new Object(); // '객체 생성자'문법
let user = {}; // '객체 리터럴' 문법-객체를 선언할 때에는 주로 이러한 방법을 사용합니다.
```

## 리터럴과 프로퍼티

- {중괄호} 안에는 '키:값'과 쌍으로 구성된 프로퍼티가 들어갑니다.

```jsx
		let user = {         // 객체 -큰 틀
			name: "John",      // 키 : "name", 값 : "Juhn"
												 // 첫번째 프로퍼티 
			age: 30            // 키 : "age" , 값 : 30
											   // 두번째 프로퍼티
};
```

- 점표기법을 이용하면 프로퍼티 값을 읽는 것도 가능합니다.

++++점표기법이란? (질문)

```jsx
// 프로퍼티 값 얻기
alert(user.name); // Jone
alert(user.age); // 30
```

- 프로퍼티 값에는 모든 자료형이 올 수 있습니다.

```jsx
user.inAdmin = true;
// 불린형 프로퍼티 추가
```


```jsx
delete user.age;
//프로퍼티 삭제 
```

- 단어를 조합해서 **프로퍼티 이름을 만들 때 따옴표로 묶어줘야 합니다.**

```jsx
let user = {
		name : "John",
		age : 30,
    "like birds" : true // 복수의 단어는 따옴표로 묶어준다.
};

let user = {
		name : "John",
    age : 30,
}

// 이런 쉼표를 가리켜 trailing or hanging 쉼표라고 부릅니다. 이런식으로 쉼표를 붙이는 경우 프로퍼티가 유사한 형태를 보일 수 있기 때문에 프로퍼티를 추가하거나 삭제 또는 이동하기가 쉬워집니다.
```


⚠**Object with const can be changed**

Please note: an object declared as `const` *can* be modified.

For instance:

```jsx
const user = { name: "John"};
user.name = "Pete"; // (*)alert(user.name); // Pete
```

It might seem that the line `(*)` would cause an error, but no. The `const` fixes the value of `user`, but not its contents.

The `const` would give an error only if we try to set `user=...` as a whole.

There’s another way to make constant object properties, we’ll cover it later in the chapter [프로퍼티 플래그와 설명자](https://ko.javascript.info/property-descriptors).

- 오브젝트를 const로 선언을 하게 되면 상수로 선언을 한 것인데도 불구하고 오브젝트 내에서 프로퍼티 값을 변경할 수 있다는 뜻입니다.

## 대괄호 표기법

- 여러 단어를 조합하여 프로퍼티 키를 만들게 된 경우에는 점 표기법을 사용하여 프로퍼티 값을 읽을 수 없습니다.

```jsx
// 문법 에러가 발생한다.
user.like birds = true

// user.like까지는 이해하지만 birds를 만나면 문법 에러를 뱉어냅니다.
// 그래서 이런 경우에는 점 표기법 대신에 대괄호 표기법이라 불리는 방법을 사용합니다.
```

```jsx
// 대괄호 표기법 사용방법

let user = {};

// set
user["like birds"] = true;

// get
alert(user["like birds"]); //true

//delete
delete user["likes birds"];

//true
```

- 변수를 키로 사용한 것과 같이 문자열 뿐만이 아니라 모든 표현식의 평가 결과를 프로퍼티 키로 사용이 가능합니다.

```jsx
let key = "likes birds";

//user["like birds"] = true;와 같다

user[key] = true;
```

```jsx
// 점 표기법은 이러한 방식이 불가능하다.

let user = {
	name: "John",
	age: 30
};

let key ="name";
alert(user.key) // undefined
```

### 계산된 프로퍼티

- 객체를 만들 때 객체 리터럴 안에 프로퍼티 키가 대괄호로 들러싸여 있는 경우에 이를 계산된 프로퍼티(computed property)라 부릅니다.

```jsx
let fruit = prompt ("어떤 과일을 구매하시겠습니까?", "apple");
let bag = {
	[fruit]: 5, // 변수 fruit에서 프로퍼티 이름을 동적으로 받아 옵니다.
};

alert (bag.apple); // fruit에 "apple"이 할당되었으므로 5가 출력됩니다.
```

- 또 다른 예시

```jsx
let fruit = prompt ("어떤 과일을 구매하시겠습니까?", "apple");
let bag ={};

// 변수 fruit을 사용해 프로퍼티 이름을 만들었습니다.
bag[fruit]=5;

// 복잡한 표현식

let fruit ="apple";
let bag = {
	[fruit + 'Computers']: 5 // bag.appleComputers = 5
};
```

- 점 표기법보다 강력한 이 대괄호 표기법은 작성하기가 번거롭다.
- 그러므로 복잡한 상황이 발생이 되었을 경우 대괄호 표기법으로 바꾸는 경우가 많습니다.

## 단축 프로퍼티

```jsx
function makeUser(name,age) {
	return {
	name : name,
	age : age,
	};// .... 등등

}

let user = makeUser("John",30);
alert(user.name); // John

// 여기서 프로퍼티 값 단축 구문을 사용하게 되면 코드를 짧게 줄일 수 있습니다.

function makeUser(name,age) {
	return {
		name, // name: name 과 같음
		age, // age: age 와 같음
		// ...
	};
}

// 일반 프로퍼티와 단축 프로퍼티를 같이 사용하는 경우

let user = {
	name, // name : name 과 같음
  age: 30
};
```

## 프로퍼티 이름의 제약사항

- 변수 이름(키)에는 'for','let','return'과 같은 예약어를 사용하면 안됩니다.
- **객체 프로퍼티에는 이런 제약이 없습니다.**

```jsx
// 예약어를 키로 사용해도 괜찮습니다.
let obj = {
	for : 1,
	let : 2,
 return: 3
};
alert( obj.for + obj.let + obj.return ); // 6
```

```jsx
// 문자형이나 심볼형에 속하지 않은 값은 문자열로 자동 형 변환됩니다.

let obj = {
	 0: "test" // "0": "test"와 동일합니다.
};

// 숫자 0 은 문자열 "0"으로 변환되기 때문에 두 얼럿 창은 같은 프로퍼티에 접근합니다,
alert(obj["0"]);//test
alert(obj[0]); // test(동일한 프로퍼티)
```

- 객체 프로퍼티 키에 쓸 수 있는 문자열에는 제약이 없지만 특별한 대우를 받는 것이 있는데 그것이 바로 __proto__**입니다.**

```jsx
let obj = {};
obj.__proto__ = 5; // 숫자를 할당합니다.
alert(obj.__proto__); // [object Object] - 숫자를 할당했지만 값은 객체가 되었습니다. 의도한대로 동작하지 않네요.
```

- 오브젝트의 DNA같은 것이라고 보면 되며 자바스크립트의 모든 오브젝트는 Object 객체의 상속판이라고 보면 되고 이것은 Object객체로부터 물려받은 것이다.

## 'in'연산자로 프로퍼티 존재 여부 확인하기

- 다른 언어와 달리 **존재하지 않는 프로퍼티에 접근하려고 해도 에러가 발생하지 않으며 undefined를 반환**합니다.

```jsx
let user = {};

alert( user.noSuchProperty === undefined ); // true는 '프로퍼티가 존재하지 않음'을 의미합니다.
```

```jsx
// 연산자 in을 사용한 경우
"Key" in object

ex)

let user ={name :"John", age:30};

alert("age" in user); // user.age가 존재하므로 true가 출력됩니다.
alert("blabla" in user) // user.blabla는 존재하지 않기 때문에 false가 출력됩니다.

//in 왼쪽에 프로퍼티 이름을 따옴표로 감싼 문자열로 하지 않을 경우

let user = {age:30};

let key = "age";
alert( key in user ) ; // true, 변수 key에 저장된 값("age")을 사용해 프로퍼티 존재 여부를 확인합니다.
```

- undefined랑 비교해도 되는데 in연산자를 사용해야 하는 이유는 일치 연산자를 사용해서 프로퍼티 존재 여부를 알아내는 방법 ("===undefined")은 꽤 잘 동작하는 편입니다.
- 여기서 in을 사용하게 되면 프로퍼티 존재 여부를 제대로 판별할 수 있습니다.

```jsx
let obj = {
  test: undefined
};

alert( obj.test ); // 값이 `undefined`이므로, 얼럿 창엔 undefined가 출력됩니다. 그런데 프로퍼티 test는 존재합니다.

alert( "test" in obj ); // `in`을 사용하면 프로퍼티 유무를 제대로 확인할 수 있습니다(true가 출력됨).
```

- undefined는 변수가 정의되어 있지만 값이 할당되지 않은 경우에 쓰이기 때문에 undefined인 경우가 흔치 않습니다.
- 값을 알 수 없거나 비어있다고 할때에는 null을 사용합니다

## 'for...in' 반복문

- 이 반복문을 사용하면 객체의 모든 키를 순회할 수 있습니다.
- for(;;) 반복문과는 완전히 다릅니다.

```jsx
for (key in object) {
	// 각 프로퍼티 키(key)를 이용하여 본문(body)을 실행합니다.
}

ex)

let user = {
	name: "John",
	age: 30,
 isAdmin: true
};

for (let key in user) {
	// 키-반복 변수명은 자유롭게 정할 수 있습니다.
alert (key); // name,age,inAdmin
// 키에 해당하는 값
alert (user[key]); //John,30,true
}
```

## 객체 정렬 방식

- 국제전화 나라번호가 담겨 있습니다.

```jsx
let codes = {
	"49":"독일",
	"41": "스위스",
	"44": "영국",
	// ..,
"1" : "미국"
};

for (let code in codes) {
  alert(code); // 1, 41, 44, 49
}

// 이 이유는 나라 번호 (키)가 정수여서 1,41,44,49순으로 프로퍼티가 자동으로 정렬이 되었기 때문입니다.
```

정수 프로퍼티라는 용어는 변형 없이 정수에서 왔다갔아 할 수 있는 문자열을 뜻합니다.

```jsx
// 함수 Math.trunc는 소수점 아래를 버리고 숫자의 정수부만 반환합니다.
alert( String(Math.trunc(Number("49"))) ); // '49'가 출력됩니다. 기존에 입력한 값과 같으므로 정수 프로퍼티입니다.
alert( String(Math.trunc(Number("+49"))) ); // '49'가 출력됩니다. 기존에 입력한 값(+49)과 다르므로 정수 프로퍼티가 아닙니다.
alert( String(Math.trunc(Number("1.2"))) ); // '1'이 출력됩니다. 기존에 입력한 값(1.2)과 다르므로 정수 프로퍼티가 아닙니다.
```

```jsx
let user = {
  name: "John",
  surname: "Smith"
};
user.age = 25; // 프로퍼티를 하나 추가합니다.

// 정수 프로퍼티가 아닌 프로퍼티는 추가된 순서대로 나열됩니다.
for (let prop in user) {
  alert( prop ); // name, surname, age
}
```

```jsx
let codes = {
  "+49": "독일",
  "+41": "스위스",
  "+44": "영국",
  // ..,
  "+1": "미국"
};

for (let code in codes) {
  alert( +code ); // 49, 41, 44, 1
}
```

- 이제 원하는 대로 독일 나라 번호가 가장 먼저 출력되는 것을 확인할 수 있습니다.

## Summary

- 객체는 몇 가지 특수한 기능을 가진 연관 배열(associative array)입니다.
- 객체는 프로퍼티(키-값 쌍)를 저장합니다.
- 프로퍼티 키는 문자열이나 심볼이어야 합니다. 보통은 문자열입니다.
- 값은 어떤 자료형도 가능합니다.

## 자바스크립트에는 일반 객체 이외에도 다양한 종류의 객체가 있습니다.

> Array – 정렬된 데이터 컬렉션을 저장할 때 쓰임
Date – 날짜와 시간 정보를 저장할 때 쓰임
Error – 에러 정보를 저장할 때 쓰임
기타 등등

객체마다 고유의 기능을 제공하는데, 이에 대해선 추후 학습하겠습니다. 사람들은 종종 **'Array 타입’이나 'Date 타입’이라는 용어**를 쓰곤 합니다. 사실 **Array와 Date는 독립적인 자료형이 아니라 '객체’형**에 속합니다. 객체에 다양한 기능을 넣어 확장한 또 다른 객체이죠. → 풀어서 쓰면?

- 모든 오브젝트는 Object를 기반으로 확장이 되는 것이라고 보면 된다. 그렇기 때문에 **'Array 타입’이나 'Date 타입’도 자료형이 아니다.**
- 그렇지만 개발할때 편하게 쓰도록 만들어져 있으며 당연시하게 쓰이고 있으므로 Data형이나 Array형 같은 말로 부르는 것입니다.

### 중복되는 코드 단순화하기

```jsx
// 1주차 App 컴포넌트
...
    <button onClick={handleClick} value="스페이스 엑스">
            스페이스 엑스
        </button>
        <button onClick={handleClick} value="테슬라">
            테슬라
        </button>
        <button onClick={handleClick} value="보링 컴퍼니">
            보링 컴퍼니
        </button>
        <button onClick={handleClick} value="솔라시티">
            솔라시티
        </button>
...
```
- `button` 태그들을 보면 값만 다를 뿐인데 코드가 중복됩니다.
- 이럴때는 **반복문을 통해서 중복되는 코드를 줄이라고 배웠는데 JSX에서는 for문(statement)을 사용할 수 없습니다.**
- **map을 통해서 코드를 조금 더 단순화** 해보도록 하겠습니다.

## 배열 내장 함수

# map 함수의 원리

- `map`은 배열을 순회 하면서 **인자로 넣어준 함수를 실행** 합니다.

```jsx
const fruits = ["사과", "감", "배", "딸기"];

// map에 넣어줄 함수
function sayHello() {
    console.log("hello");
}
// map을 통해 fruits 순회하기
fruits.map(sayHello);
// hello(4) 출력
```
- 전체 배열을 순회하면서 인자에 넣어준 함수 `sayHello`를 실행시키기 때문에 콘솔창에 hello가 4번 찍히는 것을 볼 수 있습니다.

```jsx
const fruits = ["사과", "감", "배", "딸기"];

// map에 넣어줄 함수
function sayFruit(element) {
    console.log(element);
}
// map을 통해 fruits 순회하기
fruits.map(sayFruit);
// 사과,감,배,딸기 출력
```
- 리스트를 순회할 때 map 안에 있는 함수를 첫번째 인자에 리스트 내 요소들이 들어간 것을 볼 수 있습니다.
```jsx
const fruits = ["사과","감","배","딸기"];
// fruits로 리스트 만들기
for (let i = 0; i < fruits.length; i++) {
    // for 문을 사용하여 fruits 배열을 순회함
    console.log(fruits[i]);
    // fruits[i] 안에 담긴 배열을 출력
}

```

# 화살표 함수로 코드 간결하게 만들기

- `arrow function`을 통해서 좀 더 간결하게 코드를 바꿀 수 있습니다.

```jsx
const fruits = ["사과", "감", "배", "딸기"];

// 일반 함수를 통해서 순회하기
fruits.map(function sayFruit(element) {
    console.log(element);
});

// 화살표 함수를 통해서 순회하기
fruits.map((fruit) => console.log(fruit));

```
- `element`를 `fruit`로 바꾸어 주었는데 map의 경우 함수의 첫번째 인자로 리스트의 요소를 넣어주므로 이름은 크게 상관이 없습니다.
- 그래서 이름을 `element`에서 `fruit`으로 변경하였습니다.

# map함수에서의 return

```jsx
const fruits = ["사과", "감", "배", "딸기"];
const result = fruits.map((fruit) => fruit + "는 맛있다");
console.log(result);
// 사과는 맛있다, 감는 맛있다, 배는 맛있다,딸기는 맛있다.
```
- **map함수를 사용하면 리스트의 각 요소에 짝을 맞추어 원하는 작업**을 할 수 있습니다.

```jsx
// for문으로 구현한 코드
const fruits = ["사과", "감", "배", "딸기"]; // 바뀌지 않는 것 변수 재선언이 되지 않는다.
let newFruits = []; // 빈 배열로 담기
for (let i = 0; i < fruits.length; i++) { // for문을 통하여 fruits를 순회함
    newFruits.push(fruits[i] + "는 맛있다"); // 빈 배열에 fruits + "는 맛있다"를 넣는다.
}
console.log(newFruits); // newFruits 출력

```

## map함수를 활용하여 코드 단순화하기

```jsx
// app.js
const quiz = {
    question: "일론 머스크의 우주 탐사 기업 이름은?",
    answers: [
        { text: "스페이스 엑스", isCorrect: true },
        { text: "테슬라", isCorrect: false },
        { text: "보링 컴퍼니", isCorrect: false },
        { text: "솔라시티", isCorrect: false },
    ],
};
```
```jsx
// app.js
import React from "react";
import "./App.css";

function App() {
    const quiz = {
        question: "일론 머스크의 우주 탐사 기업 이름은?",
        answers: [
            { text: "스페이스 엑스", isCorrect: true },
            { text: "테슬라", isCorrect: false },
            { text: "보링 컴퍼니", isCorrect: false },
            { text: "솔라시티", isCorrect: false },
        ],
    };

    const handleClick = (e) => {
        const answer = e.target.value;
        if (answer === "스페이스 엑스") {
            alert("정답 👏👏👏");
        } else {
            alert("오답 😣😣😣");
        }
    };

    return (
        <div className="container">
            <div className="app">
                <div className="question-section">
                    <h1 className="question-header">
                        <span>1</span>/4
                    </h1>
                    // 퀴즈 명을 {quiz.question}을 활용하여 바꿔줬습니다.
                    <div className="question-text">{quiz.question}</div>
                </div>
                <div className="answer-section">
                // 버튼의 경우 map을 활용하여 quiz.answers를 한바퀴 돌며 button 컴포넌트를 만들어줬습니다.
                    {quiz.answers.map((answer) => (
                        <button value={answer.text} onClick={handleClick}>
                            {answer.text}
                        </button>
                    ))}
                </div>
            </div>
        </div>
    );
}

export default App;
```

## DRY 원칙의 의미

- 프로그래밍에 있어서 무의미한 반복은 금기시된다.
- 소스 코드 중복,무분별한 낭비, 반복은 누군가의 시간,노력,효율을 다방면에 걸쳐서 큰 손실을 준다.
- 객체지향의 큰 장점은 반복을 최소화하도록 설계되었다는 점이다.
- 정보은닉,캡슐화,추상화의 원칙과 같은 객체 지향의 특징들은 불필요한 코드를 줄이고 중복을 최소화하는데 여러 부분에서 기여한다.

## 배열 내장함수

# forEach

- `forEach`는 가장 쉬운 배열 내장함수입니다.

```jsx
const superheroes = ['아이언맨', '캡틴 아메리카', '토르', '닥터 스트레인지'];
```

- 배열안에 있는 모든 원소들을 모두 출력해야 한다면 for문을 사용하여 다음과 같이 구현할 수 있습니다.

```jsx
const superheroes = ['아이언맨', '캡틴 아메리카', '토르', '닥터 스트레인지'];

for (let i = 0; i < superheroes.length; i++) {
  console.log(superheroes[i]);
}
```
- forEach 함수를 사용하면 다음과 같이 구현할 수 있습니다.

```jsx
const superheroes = ['아이언맨', '캡틴 아메리카', '토르', '닥터 스트레인지'];

superheroes.forEach(hero => {
  console.log(hero);
});
```
- forEach 함수의 파라미터로는 각 원소에 대해서 처리하고 싶은 코드를 함수로 넣어줍니다.
- 함수의 파라미터 hero는 각 원소를 가르키게 됩니다.
- 함수형태의 파라미터를 전달하는 것을 콜백함수라 부릅니다. 함수를 등록하면 forEach가 실행됩니다.

# map

- `map`은 배열 안의 각 원소를 변환할 때 사용되며 이 과정에서 새로운 배열이 만들어집니다.

```jsx
const array = [1, 2, 3, 4, 5, 6, 7, 8];
```

```jsx
// map 함수를 사용하지 않는 경우

const array = [1, 2, 3, 4, 5, 6, 7, 8];
const squared = [];
for (let i = 0; i < array.length; i++) {
  squared.push(array[i] * array[i]);
}

console.log(squared);
```

```jsx
// forEach를 사용하는 경우
const array = [1, 2, 3, 4, 5, 6, 7, 8];

const squared = [];

array.forEach(n => {
  squared.push(n * n);
});

console.log(squared);
```
```jsx
// map을 사용하는 경우
const array = [1, 2, 3, 4, 5, 6, 7, 8];

const square = n => n * n;
const squared = array.map(square);
console.log(squared);
```
- map함수의 파라미터로는 변화를 주는 함수를 전달해줍니다. 이를 변화함수라 합니다.
- 변화함수 square는 파라미터 n을 받아와서 이를 제곱합니다.
- array.map함수를 사용 할 떄 square를 변화함수로 사용함으로써 내부의 모든 값에 대하여 제곱을 해서 새로운 배열을 생성합니다.

```jsx
const squared = array.map(n => n * n);
console.log(squared);
```

# indexOf

- `indexOf`는 원하는 항목이 몇번째 원소인지 찾아주는 함수입니다.

```jsx
const superheroes = ['아이언맨', '캡틴 아메리카', '토르', '닥터 스트레인지'];

// 토르가 몇번째 함수인지 알고 싶을 때?
const index = superheroes.indexOf('토르');
console.log(index);
// 결과값 : 2
```
# findIndex

- 배열 안에 있는 값이 숫자,문자열 또는 불리언이라고 한다면 찾고자 하는 항목이 몇번째 원소인지 알아낼려면 indexOf를 사용해야 합니다.
- 배열 안에 있는 값이 객체이거나 배열이라면 indexOf로 찾을 수 없습니다.

```jsx
const todos = [
  {
    id: 1,
    text: '자바스크립트 입문',
    done: true
  },
  {
    id: 2,
    text: '함수 배우기',
    done: true
  },
  {
    id: 3,
    text: '객체와 배열 배우기',
    done: true
  },
  {
    id: 4,
    text: '배열 내장함수 배우기',
    done: false
  }
];
```
- 여기서 id가 3인 객체가 몇번째인지 찾으려면 `findIndex` 함수에 검사하고자 하는 조건을 반환하는 함수를 넣어서 찾을 수 있습니다.

```jsx
const todos = [
  {
    id: 1,
    text: '자바스크립트 입문',
    done: true
  },
  {
    id: 2,
    text: '함수 배우기',
    done: true
  },
  {
    id: 3,
    text: '객체와 배열 배우기',
    done: true
  },
  {
    id: 4,
    text: '배열 내장함수 배우기',
    done: false
  }
];

const index = todos.findIndex(todo => todo.id === 3);
console.log(index);

// 결과값 2
```

# find

- `find`함수는 `findIndex`랑 비슷한데 찾아낸 값이 몇번째인지 알아내는 것이 아니라 찾아낸 값 자체를 반환합니다.

```jsx
const todos = [
  {
    id: 1,
    text: '자바스크립트 입문',
    done: true
  },
  {
    id: 2,
    text: '함수 배우기',
    done: true
  },
  {
    id: 3,
    text: '객체와 배열 배우기',
    done: true
  },
  {
    id: 4,
    text: '배열 내장함수 배우기',
    done: false
  }
];

const todo = todos.find(todo => todo.id === 3);
console.log(todo);
// 결과 값 : {id: 3, text: "객체와 배열 배우기", done: true}
```
# filter

- filter 함수는 배열에서 특정 조건을 만족하는 값들만 따로 추출하여 새로운 배열을 만듭니다.

```jsx
const todos = [
  {
    id: 1,
    text: '자바스크립트 입문',
    done: true
  },
  {
    id: 2,
    text: '함수 배우기',
    done: true
  },
  {
    id: 3,
    text: '객체와 배열 배우기',
    done: true
  },
  {
    id: 4,
    text: '배열 내장함수 배우기',
    done: false
  }
];

const tasksNotDone = todos.filter(todo => todo.done === false);
console.log(tasksNotDone);

```

```jsx
// 결과값
[
  {
    id: 4,
    text: '배열 내장 함수 배우기',
    done: false
  }
];
```
- filter 함수에 넣는 파라미터는 조건을 검사하는 함수를 넣어주며 이 함수를 파라미터로 각 원소의 값을 받아오게 됩니다.
- 우리가 작성한 코드는 이렇게 입력할 수 있습니다.

```jsx
const tasksNotDone = todos.filter(todo => !todo.done);
```
- filter에 넣어준 함수에서 true를 반환하면 새로운 배열에 추가를 해줍니다.
- todo.done값이 false라면 !false가 되고 이 값은 true이기 때문에 todo.done === false와 똑같이 작동하게 됩니다.

# splice

- splice는 배열에서 특정 항목을 제거할 때 사용합니다.

```jsx
const numbers = [10, 20, 30, 40];

// 30을 지운다고 가정하면?

const numbers = [10, 20, 30, 40];
const index = numbers.indexOf(30);
numbers.splice(index, 1);
console.log(numbers);
// 결과값 : 10,20,40
```
- splice를 사용할 때 첫번째 파라미터는 어떤 인덱스를 지울 것인지를 의미한다.
- 두번째 파라미터는 인덱스로부터 몇개를 지울지를 의미한다.

# shift 와 pop

- `shift`와 `pop`은 비슷하지만 다릅니다.
- `shift`는 첫번째 원소를 배열에서 추출해줍니다. (추출과정에서 배열에서 해당 원소를 사라집니다.)

```jsx
const numbers = [10, 20, 30, 40];
const value = numbers.shift();
console.log(value);
console.log(numbers);
``` 
```jsx
// 결과값
10
[20, 30, 40]
```
```jsx
// pop을 한 결과
const numbers = [10, 20, 30, 40];
const value = numbers.pop();
console.log(value);
console.log(numbers);
```
```jsx
// 결과값
40
[10, 20, 30]
```
- `pop`은 `push`의 반대로 생각하시면 됩니다.
- `push`는 배열의 맨 마지막에 새 항목을 추가합니다.
- `pop`은 맨 마지막 항목을 추출합니다.

# unshift

- `unshift`는 `shift`의 반대입니다.
- 배열의 맨 앞에 새 원소를 추가합니다.

```jsx
const numbers = [10, 20, 30, 40];
numbers.unshift(5);
console.log(numbers);
// [5, 10, 20, 30, 40]
```

# concat

- `concat`은 여러개의 배열을 하나의 배열로 합쳐줍니다.
```jsx
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const concated = arr1.concat(arr2);

console.log(concated);
// [1, 2, 3, 4, 5, 6 ];
```
- `concat` 함수는 arr1과 arr2에 변화를 주지 않습니다.

# join

- `join`은 배열 안의 값들을 문자열 형태로 합쳐줍니다.

```jsx
const array = [1, 2, 3, 4, 5];
console.log(array.join()); // 1,2,3,4,5
console.log(array.join(' ')); // 1 2 3 4 5
console.log(array.join(', ')); // 1, 2, 3, 4, 5
```
# reduce

```jsx
// 주어진 배열에 대해서 총 합을 구할 때
const numbers = [1, 2, 3, 4, 5];

let sum = 0; // sum 선언
numbers.forEach(n => {
    // forEach를 통해서 계속 덧셈을 한다.
  sum += n;
});
console.log(sum);
// 결과값 15
```
```jsx
// reduce함수를 사용
const numbers = [1, 2, 3, 4, 5];
let sum = numbers.reduce((accumulator, current) => accumulator + current, 0);
// reduce 함수에서는 두개의 파라미터를 전달합니다.
// 첫번째 파라미터에서는 accumulator , currentr를 가져와서 결과를 반환하는 콜백함수
// 두번째 파라미터는 reduce 함수에서 사용할 초깃값
// accumulator는 누적된 값을 의미 

console.log(sum);
```

```jsx
// 작성 함수를 수정해보자
const numbers = [1, 2, 3, 4, 5];
let sum = numbers.reduce((accumulator, current) => {
  console.log({ accumulator, current });
  return accumulator + current;
}, 0);

console.log(sum);
```

- 배열을 처음부터 끝까지 반복하면서 전달한 콜백함수가 호출이 된다.
- 처음에는  accumulator값이 0입니다. 이 값이 0인 이유는 두번째 파라미터인 초깃값으로 0을 설정했기 때문입니다.
- 콜백 함수가 호출되면 0 + 1을 사용해서 1이 반환됩니다.
- 1이 반환되면 다음 번 콜백함수가 호출될때 accumulator값으로 사용됩니다.
- 콜백함수가 두번째로 호출될 때 1+2를 해서 3이되고 이 값이 세번째로 호출될 때  accumulator가 됩니다.
- 누적된 결과물이 15가 된다.

```jsx
// 평균을 계산하는 방법
// 가장 마지막 숫자를 더하고 나서 배열의 length로 나누어 줘야 합니다.
const numbers = [1, 2, 3, 4, 5];
let sum = numbers.reduce((accumulator, current, index, array) => {
    // 추가 파라미터 index,array를 받는다.
    // index는 현재 처리하고 있는 항목이 몇번째인지 가르킨다.
    // array는 현재 처리하고 있는 배열 자신을 의미한다.
  if (index === array.length - 1) {
    return (accumulator + current) / array.length;
  }
  return accumulator + current;
}, 0);

console.log(sum);
// 결과값 3

```

## 정답 체크 함수 최적화하기

```jsx
// app.js
...
const handleClick = (e) => {
    const answer = e.target.value;
    if (answer === "스페이스 엑스") {
        alert("정답 👏👏👏");
    } else {
        alert("오답 😣😣😣");
    }
};
...
```
- `handleClick` 함수에서 `answer === "스페이스 엑스"`처럼 정답 값을 일일히 확인하고 있습니다.
- 이렇게 되면 다른 퀴즈 문제가 추가가 될 수 없다는 단점이 있습니다.

## 이벤트 핸들러에 매개변수 넘기기

```jsx
// app.js
...
const handleClick = (isCorrect) => {
    if (isCorrect){
        alert("정답 👏👏👏");
    } else {
        alert("오답 😣😣😣");
    }
};
...
```
- `event`를 받는 대신에 누른 버튼이 정답인지 아닌지 확인하는 `isCorrect`값을 받고 기준으로 `alert`창을 띄어 주려고 합니다.

```jsx
// app.js
const quiz = {
        question: "일론 머스크의 우주 탐사 기업 이름은?",
        answers: [
            { text: "스페이스 엑스", isCorrect: true },
            { text: "테슬라", isCorrect: false },
            { text: "보링 컴퍼니", isCorrect: false },
            { text: "솔라시티", isCorrect: false },
        ],
    };
...
<button value={answer.text} onClick={handleClick}>
    {answer.text}
</button>
...
```
- `answer`안에 `isCorrect`값이 있으므로 이 값을 `handleClick`함수에 보내면 될 것 같습니다.

```jsx
// 화살표로 한 번 더 감싸 주면 handleClick에 우리가 원하는 값을 전달할 수 있습니다.

// app.js
...
<button value={answer.text} onClick={() => handleClick(answer.isCorrect)}>
    {answer.text}
</button>
...
```

## 이렇게 하면 안됩니다.

- **화살표 함수로 감싸지 않고 바로 보내주면 안되는 것일까?**
```jsx
// app.js
...
<button value={answer.text} onClick={handleClick(answer.isCorrect)}>
    {answer.text}
</button>
...
```
- 이렇게 하게 되면 최초에 `button` 컴포넌트를 그릴 때 바로 함수를 실행하게 됩니다.
- 그렇게 됨에 따라서 화면을 다 그리지 못한 채 `alert`창이 떠서 아무것도 못하게 됩니다.
- **매개변수로 값을 넘길 때는 항상 함수로 감싸야 합니다.**

## 문제 추가하기

```jsx
// App.js
...
const quizzes = [{
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
    }
]
...
function App(){
    ...
}
...
```

- `quizzes` 변수에 여러 퀴즈가 담긴 `list`를 저장해주었습니다.
- 현재 문제가 몇 번인지 확인하기 위해 `id`값도 순서대로 넣어주었습니다.

## 여러 퀴즈 랜더링하기

```jsx
// app.js
// quizzes 변수로 코드를 변경하기
// 퀴즈 제목에 있던 숫자도 퀴즈에 따라 변경되도록 바꾸기
function App(){
    ...
    <span>{quizzes[0].id}</span>/{quizzes.length}
    ...

    ...
    <div className="question-text">{quizzes[0].question}</div>
    ...

    ...
    {quizzes[0].answers.map((answer) => (
                            <button
                                value={answer.text}
                                onClick={() => handleClick(answer.isCorrect)}
                            >
                                {answer.text}
                            </button>
    ))}
    ...
}
```
## index 값을 변수로 관리하기

```jsx
// app.js
function App(){
    let currentNo = 0;
    // currentNo 변수 만들기

    ...
    const handleClick = (isCorrect) => {
            if (isCorrect) {
                alert("정답 👏👏👏");
            } else {
                alert("오답 😣😣😣");
            }
            currentNo = currentNo + 1;
            // 버튼을 클릭할때 currentNo에 1을 더하기
        };
    ...

  ...
    <div className="container">
        <div className="app">
            <div className="question-section">
                <h1 className="question-header">
                    <span>{quizzes[currentNo].id}</span>/{quizzes.length}
                </h1>
                <div className="question-text">{quizzes[currentNo].question}</div>
            </div>
            <div className="answer-section">
                {quizzes[currentNo].answers.map((answer) => (
                    <button
                        value={answer.text}
                        onClick={() => handleClick(answer.isCorrect)}
                    >
                        {answer.text}
                    </button>
                ))}
            </div>
        </div>
    </div>
  ...
}
```

 - 이 코드는 아무런 일도 일어나지 않는다 이유는 `currentNo`의 값이 바뀌긴 하였지만 `React`가 컴포넌트를 다시 렌더링 해주지 않기 때문입니다.
 - 내가 원하는 값이 변경되었을 때 컴포넌트를 다시 렌더링 시켜주려면 이 때 사용하는 것이 바로 state입니다.

 ## 컴포넌트 내부에서 관리하는 변수 (state)

 - state는 컴포넌트 내부에서 관리되고 컴포넌트 렌더링에 영향을 미치는 객체입니다.
 - **state가 변경되면 state가 속한 컴포넌트가 다시 렌더링됩니다.**

 # state 사용하기 (useState hooks)
 
 - `state`를 사용하기 위해서 `useState`함수를 사용해야 합니다.
 - 이번에는 `useState`를 활용해서 `currentNo`를 다뤄보겠습니다.

```jsx
const state = useState(0);
const currentNo = state[0];
const setCurrentNo = state[1];
```
- `useState`는 인자로 `state`의 초기값을 받습니다.
- `currentNo`의 초기 값은 0이므로 `useState`에 0을 넣어주었습니다.
- `useState`는 결과 값으로 `state`변수와 `state`를 갱신할 수 있는 함수를 리스트에 담아서 반환해줍니다.

- 위 코드를 배열 구조 분해 할당으로 아래처럼 간단하게 사용할 수 있습니다.

```jsx
const [currentNo, setCurrentNo] = useState(0);
```

# state사용시 주의사항
- 직접 `state`를 수정하지 마세요
    - 직접 `state`를 수정하는 경우, React가 변경을 인지하지 못해 `rendering`이 되지 않습니다.
- 독립적인 `state`가 있다면 되도록이면 분리해서 사용하세요.
    - 여러 개의 `state`가 있어도 상관 없습니다. 독립적인 값들일 경우 분리하는 것이 추후 관리에 유리합니다.

## state를 활용하여 퀴즈를 다시 렌더링 시키기

```jsx
// app.js
// useState 함수 import 하기
import React, {useState} from "react";

function App(){
    // currentNo 셋팅하기
    ...
    const [currentNo, setCurrentNo] = useState(0);
    ...

    ...
    const handleClick = (isCorrect) => {
            if (isCorrect) {
                alert("정답 👏👏👏");
            } else {
                alert("오답 😣😣😣");
            }
            setCurrentNo(currentNo + 1);
    };
    ...

    ...
    <div className="container">
        <div className="app">
            <div className="question-section">
                <h1 className="question-header">
                    <span>{quizzes[currentNo].id}</span>/{quizzes.length}
                </h1>
                <div className="question-text">{quizzes[currentNo].question}</div>
            </div>
            <div className="answer-section">
                {quizzes[currentNo].answers.map((answer) => (
                    <button
                        value={answer.text}
                        onClick={() => handleClick(answer.isCorrect)}
                    >
                        {answer.text}
                    </button>
                ))}
            </div>
        </div>
    </div>
    ...
}
```

- 이렇게 실행을 하게 되면 마지막 문제에서 다음 문제가 없기 때문에 에러가 발생한다.

## useState의 함수형 업데이트

- 한 가지 다루지 않은 부분은 기존의 값에서 업데이트를 할 때는 아래와 같이 함수형으로 업데이트를 할 수 있습니다.
- 함수형으로 쓰는 이유는 `useState`의 비동기적 특성으로 인해 비효율이 발생하는데 함수형으로 사용하면 그것을 회피할 수 있습니다.

```jsx
// app.js
const handleClick = (isCorrect) => {
    if (isCorrect) {
        alert("정답 👏👏👏");
    } else {
        alert("오답 😣😣😣");
    }
    setCurrentNo((currentNo) => currentNo + 1);
};
```

## React의 setState() 정확히 사용하기

- setState()가 동작하지 않는 두가지 상황은?
    - 동일한 state를 변경하는 setState()를 연속적으로 사용
    - setState()를 실행한 뒤에 곧 바로 api 호출을 보냈을 때

1. 동일한 state를 변경하는 setState()를 연속적으로 사용
- 1) 여러 개의 메뉴를 주문할 수 있기 때문에 클릭하면 주문에 추가된다.
- 2) 이미 주문에 포함된 메뉴를 다시 클릭하면 삭제된다.
- 3) ‘선택하지 않음’을 클릭하면 주문한 모든 메뉴가 삭제되고, ‘선택하지 않음’이 추가된다.
- 4) ‘선택하지 않음’이 클릭된 상황에서 다른 메뉴를 클릭하면, ‘선택하지 않음’이 삭제되고, 클릭한 메뉴가 주문에 추가된다.

```jsx
export default function App() {
  const sideMenus = [
    "감자 튀김 🍟",
    "콜라 🥤",
    "애플 파이 🥧",
    "소프트 아이스크림 🍦",
    "선택하지 않음"
  ];
  const [orders, setOrders] = useState([]);

  const onClickHandler = selectedItem => {
    if (selectedItem === "선택하지 않음") {
      setOrders([]);
    }

    if (orders.includes(selectedItem)) {
      setOrders(orders.filter(order => order !== selectedItem));
      return;
    }

    if (orders.includes("선택하지 않음")) {
      setOrders(orders.filter(order => order !== "선택하지 않음"));
    }
    setOrders([...orders, selectedItem]);
  };

  return (
    <div className="App">
      <h3>사이드 메뉴를 선택하세요.</h3>
      <ul className="menu-group">
        {sideMenus.map((sideMenu, idx) => (
          <li
            className={
              orders.find(order => order === sideMenu)
                ? "menu-item active"
                : "menu-item"
            }
            onClick={() => onClickHandler(sideMenu)}
            key={idx}
          >
            {sideMenu}
          </li>
        ))}
      </ul>
    </div>
  );
}
```
- 여기서 3번째와 4번째 케이스에서 원하는 동작이 나오지 않는다.

3. ‘선택하지 않음’을 클릭하면 주문한 모든 메뉴가 삭제되고, ‘선택하지 않음’이 추가된다.
```jsx
// setOrder([]) 로 배열을 초기화 하고, setOrders([...orders, selectedItem]);로 ‘선택하지 않음’이 들어간다.
// 배열이 초기화 되지 않고, ‘선택하지 않음’이 추가된다.
```

4. ‘선택하지 않음’이 클릭된 상황에서 다른 메뉴를 클릭하면, ‘선택하지 않음’이 삭제되고, 클릭한 메뉴가 주문에 추가된다.
```jsx
// setOrders(orders.filter(order => order !== "선택하지 않음"));로 배열에서 ‘선택하지 않음’ 이 삭제되고, setOrders(orders => [...orders, selectedItem]);로 클릭한 메뉴가 주문에 추가된다.
// 배열에서 ‘선택하지 않음’이 삭제되지 않고, 클릭한 메뉴가 추가된다.
```

- 1) setState()는 비동기로 처리된다.
- 2) setState()를 연속적으로 호출하면 Batch 처리를 한다.
- 3) state는 객체이다.

- 리액트는 setState가 연속적으로 호출이 되면 작업을 한 번에 할 수 없을까를 생각하고 전달받은 state를 합치는 merging 작업을 수행한다
- 그 후에 한 번에 setState()를 한다.

# merging은 어떻게 동작할까?

- 3 번째 케이스의 동작을 살펴본다면 다음과 같다.

```jsx
// 배열을 초기화 setOrder([])
// 선택하지 않음을 추가하는 부분 -> etOrders([...orders, selectedItem]);
const newState = Object.assign(
  { orders : ["감자 튀김 🍟", "콜라 🥤"] },
  { orders : [] },
  { orders : [ ...orders, "선택하지 않음"]}
)

setOrders(newState)
// { orders : [ ...orders, "선택하지 않음"]}의 마지막 명령어가 실행된다.
```
- react 코드를 까보면 setState() 함수는
    - 1) 새로운 state 객체를 받을 수 있다.
    - 2) 이전 state객체를 인자로 받고 새로운 state객체를 반환하는 함수를 받을 수 있다.
- 2) 의 방법으로 했을 때 여러번의 setState()를 문제없이 실행할 수 있다.

- setState()가 비동기적으로 동작한다는 것에는 변함이 없다.
- 인자로 넘겨 받는 함수들은 Queue에 저장되어서 순서대로 실행된다.
- 첫번째 함수가 실행된 후 리턴하는 업데이트가 된 state가 두 번째 함수의 인자로 들어가는 방식
- 'state의 최신 상태가 유지된다.'

```jsx
//  onClickHandler의 내부 로직을 함수형 setState() 로 변경
const onClickHandler = selectedItem => {
    if (selectedItem === "선택하지 않음") {
      setOrders(orders => []);
    }

    if (orders.includes(selectedItem)) {
      setOrders(orders => orders.filter(order => order !== selectedItem));
      return;
    }

    if (orders.includes("선택하지 않음")) {
      setOrders(orders => orders.filter(order => order !== "선택하지 않음"));
    }
    setOrders(orders => [...orders, selectedItem]);
  };
```

```jsx
type SetStateAction<S> = S | ((prevState: S) => S);
type Reducer<S, A> = (prevState: S, action: A) => S;
```
- types/react 모듈의 index.d.ts을 살펴보면 함수형 setState와 Reducer의 내부 로직이 같은 것을 확인할 수 있다.
- **상태를 관리하는 로직이 어느 정도 수준 이상으로 복잡해지면 useReducer를 쓰자**

2. setState()를 실행한 뒤에 곧 바로 api 호출을 보냈을 때

- **setState()도 비동기 / POST 요청도 비동기로 처리되며 심지어 POST 요청의 우선 순위가 더 높아** 업데이트된 상태값이 전달되지 않는 문제가 있다.
- setState()를 굳이 실행하지 않고 일반 객체로 만들어 전달하여 해결

## useState의 비동기적 속성과 batching

- 리액트에서는 `setState`를 사용하여 상태를 업데이트할 경우 업데이트 된 상태는 즉시 반영되지 않는다.
- `setState`는 비동기적으로 작동한다. 즉 리렌더링 된 후에 비로소 업데이트 된 `state`가 반영된다.
- `state`를 업데이트하는데 있어서 비동기적으로 작동하는 속성은 여러개의 `state`를 다룰 때 퍼포먼스 측면에서 유리하다.

- 여러 `state`를 동시에 업데이트 하는 경우 리액트는 `state`를 `batching`하여 업데이트를 진행한다.(batching = 전달된 오브젝트들을 하나로 합치는 작업) 
- Batching은 object composition 이라고도 불린다.

```jsx
// 자바스크립트 코드로 object composition를 살펴보기
const singleObject = Object.assign({},
  objectFromSetState1,
  objectFromSetState2,
  objectFromSetState3
);
```

```jsx
const [value, setValue] = useState(0);

setValue(value + 1);
setValue(value + 1);
setValue(value + 1);

// Expected value: 3
// Result value: 1
```
- 업데이트 된 state를 즉시 반영해야 할 때 `useEffect()`를 사용한다.

## 함수형 업데이트

- 비동기적인 방법을 해결하기 위해 `함수형 업데이트(functional update)`를 사용할 수 있다. 
- `setState`에 값을 그대로 전달하는 것이 아니라 함수를 전달하는 것이다.

```jsx
const [value, setValue] = useState(0);

setValue(prev => prev + 1);
setValue(prev => prev + 1);
setValue(prev => prev + 1);

// Expected value: 3
// Result value: 3
```
- 함수형 업데이트는 useCallback과 함께 props로 전달된 함수를 최적화할 때도 유용하게 사용할 수 있다.
- `handleClick`함수가 자식 컴포넌트에게 props를 통해서 전다 될 경우, `useCallback`을 사용해서 함수를 감싼다.

```jsx
const handleToggle = useCallback((): void => setIsClicked(!isClicked), [isClicked]);
```

- `useCallbac`과 함께 사용을 하더라도 `isClicked`에 대한 의존성 배열을 가지게 된다.
- 여기에 의존성을 없게 만들고 싶다면 함수형 업데이트를 사용할 수 있다.

```jsx
const handleToggle = useCallback((): void => setIsClicked(prev => !prev), []);
```

### 퀴즈 결과 보여주기

## 결과 페이지 만들기

```jsx
// App.js
...
function App(){
    ...
    const [showResult, setShowResult] = useState(false);
    ...
}
```
- 페이지를 보여줄지를 판단하는 `showResult`와 `state`를 만듭니다.
- 최초 페이지에 접근할 때 퀴즈를 보여줘야 하므로 `false`로 설정합니다.
- 마지막 퀴즈의 버튼을 클릭했을 때 `showResult`의 값을 `true`로 바꿔주는 코드를 작성해봅시다.

```jsx
// App.js
function App(){
    ...
    const handleClick = (isCorrect) => {
            if (isCorrect) {
                alert("정답 👏👏👏");
            } else {
                alert("오답 😣😣😣");
            }
            // 마지막 퀴즈인지 체크하기
            if (currentNo === quizzes.length - 1) {
                setShowResult(true);
                // `setShowResult`를 호출하여 `showResult`값을 `ture`로 변경해 주었습니다.
            } else {
                setCurrentNo((currentNo) => currentNo + 1);
            }
    };
    ...
}
```
```jsx

// showResult 값을 체크하여 선택적으로 컴포넌트를 렌더링하는 코드를 만들겠습니다.

// App.js
function App(){
    ...
    return <div className="container">
    // 삼항연산자를 사용함
            {showResult ? (
                // showResult가 ture인 경우 퀴즈가 끝났습니다.를 포함하고 있는
                <div className="app">
                    <h1>퀴즈가 끝났습니다.</h1>
                </div>
                // div 태그를 노출하도록 코드를 수정한다.
            ) : (
                <div className="app">
                    <div className="question-section">
                        <h1 className="question-header">
                            <span>{quizzes[currentNo].id}</span>/{quizzes.length}
                        </h1>
                        <div className="question-text">{quizzes[currentNo].question}</div>
                    </div>
                    <div className="answer-section">
                        {quizzes[currentNo].answers.map((answer) => (
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
}
```

## 결과 페이지에 점수 표시하기

- 결과 페이지에 점수를 표현하기 위해 점수를 기록할 `state`를 하나 만듭니다.

```jsx
//app.js
function App(){
    ...
    const [score, setScore] = useState(0);
    ...
}
```
- 최초의 점수는 0점이므로 0으로 `state`값을 초기화합니다.

```jsx
// 정답 or 오답을 클릭했을 때 score를 업데이트 한다.
// App.js
function App(){
    ...
    const handleClick = (isCorrect) => {
            if (isCorrect) {
                // 오답일 경우 score를 업데이트 할 필요가 없다.                
                // 정답일 경우 setScore를 통해 scroe를 업데이트
                setScore(score => score+1)
            }
            // 마지막 퀴즈인지 체크하기
            if (currentNo === quizzes.length - 1) {
                setShowResult(true);
            } else {
                setCurrentNo((currentNo) => currentNo + 1);
            }
    };
    ...
}
```

- 결과 페이지에서 현재 점수를 노출해주도록 한다.

```jsx
// App.js
function App(){
    // 0~100 점으로 스코어를 표현하기 위해 
    // parseInt를 활용해 소수점 이하 자리를 버려줬습니다.
    const convertedScore = parseInt(score/quizzes.length * 100);
    ...
    return <div className="container">
            {showResult ? (
                <div className="app">
                    <h1 class="result-header">당신의 점수는?</h1>
                    <p class="result-score">{convertedScore}</p>
                    // 결과를 보여주는 자리에 변환된 점수 convertedScore를 추가합니다.
                </div>
            ) : (
                <div className="app">
                    <div className="question-section">
                        <h1 className="question-header">
                            <span>{quizzes[currentNo].id}</span>/{quizzes.length}
                        </h1>
                        <div className="question-text">{quizzes[currentNo].question}</div>
                    </div>
                    <div className="answer-section">
                        {quizzes[currentNo].answers.map((answer) => (
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
}
```
```jsx
// App.css
...
.result-header {
    font-size: 64px;
    font-weight: bold;
    margin-bottom: 8px;
    text-align: center;
}

.result-score {
    font-size: 192px;
    margin: 40px;
    text-align: center;
    color: rgb(115, 98, 255);
}
...
```

### netlify로 배포하기

## JAM stack

- `JAM Stack`은 `JavaScript`와 `Markup`에 해당하는 HTML, CSS 과 API를 활용하여 웹 애플리케이션을 구성하고, 그리고 이 정적 리소스들을 정해진 `CDN(Content Delivery Network)`에 배포하여 관리하는 스택

## 전통적인 Web과 다른 점

- 웹 서버를 관리할 필요가 없다.
- 전통적인 웹에서는 HTML,CSS를 제공하기 위해서는 AWS나 자체 구축된 내부 서버에 Web서버와 WAS 서버를 구성해서 HTML과 CSS를 내려야 했습니다.
- JAM Stack을 사용하며 Netlify나 Surge와 같은 서비스에 github에 있는 코드를 기준으로 CDN에 배포해서 관리하므로 관리 포인트가 많이 줄어듭니다.

- 프론트와 백엔드가 분리됩니다.
- 전통적인 `SSR`에서는 서버에서 `html`을 제공하고, `API`를 제공했기 때문에 서로 간에 영향을 많이 받았습니다. 그럼에 따라 프론트엔드의 자유도가 많이 늘어났으며, 좀 더 빠르게 배포가 가능해 졌습니다.

※비슷한 용도의 서비스로 surge, vercel이 있습니다.

## netlity로 배포하기

# 회원가입

- github와 연동해서 회원가입할 것
- New site from git 클릭-> github 클릭

```
CONTINUOUS DEPLOYMENT (지속적 배포)
소프트웨어를 더 빠르고, 더 주기적으로 빌드하고, 테스트하고, 출시하는 것을 의미합니다. 
이로써 한 번에 코드가 배포됨에 따라 생기는 위험들을 효과적으로 줄일 수 있으며, 뿐만 아니라 소요되는 시간, 비용들을 
효과적으로 관리할 수 있게 됩니다. 
```

# github repository 설정하기

- `configure Netlify on GitHub` 버튼을 클릭합니다.
- `Only Select Repository`를 선택하고, 전 파트에서 올렸던 `repository`를 클릭->`Install`버튼 클릭
- `repository`가 화면에 나오면 `github`아이콘 클릭

# 브랜치 및 빌드 설정하기

- `yarn build`는 무엇인가요? React 코드를 브라우저가 이해할 수 있는 `javascript 코드로 변환해 주는 것`을 의미합니다.
- `CRA(create react app)`에 포함되어 있는 기능
- `yarn build` 명령어를 실행하면 `build 폴더가 생기며 그 곳에 css와 변환된 js 파일이 있습니다.`
- `Deploy site` 버튼을 클릭
- `Site deploy in progress` 를 보여주며, 배포 중이며 도메인 생성되는 것을 확인할 수 있다.

```
과금요소?
한달에 빌드 시간이 300분 이상, 월 트래픽 100G 이상이 되어야 과금이 됩니다. 
```

# custom 도메인과 https 설정

- `*.netlify.app`과 같은 도메인으로 배포가 되는데 custom 도메인도 쉽게 바꿀 수 있습니다. 
- https설정도 클릭 한번에 쉽게 할 수 있습니다.

## CDN이란?(Content delivery network)

- 콘텐츠를 효율적으로 전달하기 위해 여러 노드를 가진 네트워크에 데이터를 저장하여 제공하는 시스템을 말한다. 인터넷 서비스 제공자에 직접 연결되어 데이터를 전송하므로, 콘텐츠 병목을 피할 수 있는 장점이 있다.
- 높은 사용성과 효율로 사용자에게 컨텐츠를 전달함에 있다.
- 인터넷에 존재하는 컨텐츠의 상당수를 서비스하고 있는데 이에는 웹 요소 (텍스트, 그래픽, 스크립트), 다운로드 가능한 요소 (미디어 파일, 소프트웨어, 문서), 애플리케이션 (전자상거래, 포털), 실시간 미디어, 주문형 스트리밍, 그리고 소셜 네트워크 등이 있다.

## JAM Stack 개념 정리하기

- JAM stack 은 Javascript, Api, Markup Stack 의 약자

# SPA (Single Page Application) 와 CSR (Client Side Rendering)

- 웹은 보통 완성된 `Static HTML`와 `CSS`를 네트워크로 전달 받아서, 화면을 보여준다.
- 서버가 동적 으로 HTML 을 생성할 수 있게 되면서 `Server Side Rendering(SSR)`개념이 등장
- 사용자의 요청에 따라 HTML을 만들어서 보여줄 수 있게 되었고 화면을 보여줄 수 있게 되었다.
- SSR만으로 HTML 조작이 힘들어지면서 Javascript 가 jQuery 의 만남과 함께, 중요한 역할을 하였다.
- Javascript로 화면을 조작할 일이 많이 생겼고 Javascript 와 브라우저 성능 이 더욱 발전했다.
- `Angular.js, Angular, React, Vue`와 같은 DOM 을 조작하기 쉽게 도와주는 라이브러리와 웹 프레임워크Client Side Rendering(CSR) 가 등장

- 시대가 발전하면서 서버상에서 HTML을 만들어서 전달하는 것(SSR)보다 클라이언트 상에서 HTML을 만드는것(CSR),서버 비용과 렌더링 속도 에서 유리하다.
- 정보가 없는 HTML 과 매우 큰 Javascript 그리고, 분리된 환경의 API 만으로 이루어진 Single Page Application (SPA) 환경이 이루어진다.

# 그럼에도, SSR 는 필요하다.

- SPA는 2가지 단점이 있다.
1. 사용자가 첫 의미 있는 페이지 (First meaningful paint) 를 보게 되기까지, SSR 에 비해 오래 걸린다.
2. 검색 엔진 최적화 (Search Engine Optimization, SEO) 를 맞추기가 어려워졌다.

- Javascript로 조작하는 환경은 매우 매력적이고 특히 First meaningful paint 와 SEO 가 중요했던 개발자들은 SSR 과 CSR 혼합한 형태를 만들어 내게 된다.
- Javascript 에서 사용하는 환경을 그대로 이용한 Server 인 `Angular Universal 와 NextJS 그리고 NuxtJS`이 등장
- 첫 페이지는 SSR형태로 HTML을 만들어서 보여주고 이후 모든 화면 조작과 이후 Rendering(CSR)을 Javascript가 처리하게 하는 하이브리드 형태로 발전하게 된다.

- 첫페이지는 완성된 HTML을 전달해주어 `First meaningful paint`의 속도를 높이고 검색봇이 HTML을 크롤링해서 SEO를 대응할 수 있게 된다.

# JAM Stack

- React(CSR) 와 Next.js(SSR) 같은 특정 기술로 구성된 형태를 이야기하는 것은 아니다.
- 이들을 이용해서 웹 사이트를 어떻게 구성할 것인지 관점에서 이야기해보자면 다음과 같다.
- MEAN Stack 이 MongoDB, Express.js, Angular.js, Node.js 같은 특정 기술로 이루어진 방법
- JAM Stack은 Javascript 와 API 그리고 Markup 으로 구성된 최신 웹 사이트를 구성하는 방법을 이야기 한다.

```
JavaScript

Client 의 모든 처리는 Javascript 에게 맞긴다.

API

모든 기능 및 비즈니스 로직은 재사용 가능한 API 로 추상화한다.

Markup

SSG (Static Site Generator) 나 Template Engine (Webpack 등) 을 이용하여 Markup 을 미리 생성한다.
```
# Markup

1. HTML을 직접 작성하는 방법
2. Template Engine 같은 툴을 이용
3. Jekyll (ruby), Hugo (go), Nuxt (vue), Next (react), Gatsby 같은 정적 사이트 생성기(Static Site Generator, SSG) 를 이용해서, Static HTML 을 생성
- 미리 작성된 Static HTML은 웹서버의 리소스를 쓸 필요가 없이 사용자에게 `HTML`만 전달하면 된다.

- Static HTML을 CDN을 통해서 Cache하고 배포해서 빠른 속도를 유지한다.
- 따로 HTML을 생성하지 않기 때문에 웹서버가 필요하지 않고 서버 비용이 높지 않다.

- 최대한 HTML Build을 빌드하여 Cache하고 사용자를 위한 `First meaningful paint`의 속도를 높이자는 점이다.

# 간단한 JAM Stack 과정

- gatsbyjs 와 netlify 를 이용해 웹사이트를 구축
- gatsbyjs 는 React 와 GraphQL 를 이용한 정적 사이트 생성기
- netlify 는 Javascript 코드를 빌드 하고 배포 하고 운영할 수 있게 도와주는 플랫폼

1. Github 으로 프로젝트를 관리한다.
2. Gatsbyjs (SSG) 로 정적 사이트 생성기를 구축한다.
3. Netlify 에 배포 환경을 구성한다.
4. GitHub 에 코드가 변경되면, Netlify 에서 빌드를 시작한다.
5. Netlify 로 Gatsbyjs 으로 빌드하고, 사이트을 배포한다.

- 배포된 Package는 더 이상 빌드를 위한 웹서버의 자원은 필요하지 않게 된다.
- 모든 처리는 Javascript와 API에서 이루어지게 된다.

- JAM Stack은 하나의 개념이기 때문에 특정 라이브러리나 플랫폼을 이용하지 않는다.
- 본인이 직접 빌드툴 혹은 프레임워크를 만들거나 호스팅 서버나 CDN을 운영해도 된다.

[퀴즈 앱 배포하기](https://github.com/leeyj4805/my-quiz.git)