# 인터페이스

## 인터페이스 이해

```tsx
function printLabel (labelObj: {label:string}){
		console.log(labelObj.label);
}
```

printLabel의 타입 검사를 보면 객체를 파라미터로 받은 뒤 객체 내부에 존재하는 label값이 string인지 검사하고 있다

```tsx
function printLabel (labelObj: {label:string}){
		console.log(labelObj.label);
}

let myObj = {label: "string in labelObj"};

printLabel(myObj); // "string in labelObj"
```

myObj안에 string타입의 label 프로퍼티가 들어가 있기 때문에 정상적으로 출력되는 것을 볼 수 있다

만약 객체의 내부 프로퍼티 구조가 바뀐다면 어떨까?

```tsx
function printLabel (labelObj: {label:string}){
    console.log(labelObj.label);
}

let myObj = {something: 1000, something2 : 'this is something', label: 'string in labelObj'};

printLabel(myObj); // "string in labelObj"
```

이것도 정상적으로 내용이 출력되는 것을 볼 수 있다

여기서 타입스크립트가 검사할 때 필요한 프로퍼티가 있는지, 그리고 그 프로퍼티의 타입이 잘 맞는지만을 확인하는 것을 엿 볼 수 있다

이제 위의 코드에서 인터페이스를 활용해보자

```tsx
interface Labeled {
    label : string
}

function printLabel (labelObj: Labeled){
    console.log(labelObj.label);
}

let myObj = {something: 1000, something2 : 'this is something', label: 'string in labelObj'};

printLabel(myObj);
```

Labeled 인터페이스는 이전에 검사하던 요구사항을 완전히 동일하게 수행할 수 있다

인터페이스로 파라미터를 검사한다고 해서 파라미터로 들어갈 객체가 Labeled 인터페이스를 이용해서 구현해야할 필요가 없다

ex) 

`let myObj : Labeled = {something: 1000, something2 : 'this is something', label: 'string in labelObj'};`

객체에 label이름을 가진 프로퍼티가 string 타입으로 구현되어 있으면 그 객체를 Labeled 인터페이스로 판단하고 이것을 바로 덕 타이핑이라고 부른다

# 선택적 프로퍼티

인터페이스 안의 모든 프로퍼티가 항상 필요하진 않다

몇 가지만 필요하고 다른 것들은 필요가 없을 경우가 있는데 그럴 땐  선택적 프로퍼티를 사용하면 된다

```tsx
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"});
```

사용 방법은 매우 간단한데 프로퍼티명 뒤에 ?를 붙여주면된다 

인터페이스에 해당되는 프로퍼티만 받거나, 받지 않으면서 그 외의 다른 값은 에러를 발생시키는 패턴

### 의문점

분명 위의 코드에서

```tsx
let mySquare = createSquare({color: "black", something: 123});
```

같은 코드를 쓰면 에러를 발생시키지만

```tsx
const myObj = {color:'red', something: 123};

let mySquare = createSquare(myObj);
```

첫 번째 인터페이스에서 사용한 것 처럼 객체를 만들어서 파라미터로 던지면 에러를 발생시키지 않는다

분명 덕 타이핑? 구조적 타이핑은 허용이라고 했는데 왜 에러가 발생하는 것 일까

# 읽기 전용 프로퍼티

말 그대로 읽기 전용, 제일 처음 프로퍼티를 초기화한 이후에는 수정이 불가능하다

```tsx
interface readOnly {
    readonly a: string;
    readonly b: number;
}

const neverChange : readOnly = {
    a: 'asdf',
    b: 123,
};

neverChange.a = 'asdfz'; // 에러
neverChange.b = 1000; // 에러
```

# 초과 프로퍼티 검사

이 챕터에서 내가 선택적 프로퍼티에서 가진 의문을 해결해주었다..

```tsx
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black", isTriangle: false});
```

사용자는 삼각형이 아니라는 뜻에서 의미없는 변수 isTriangle: false를 추가해 주어서 컬러가 black이고 크기가 100인 사각형을 돌려받길 원한다

하지만 타입스크립트에서는 인터페이스에 들어가있지 않은 값이기 때문에 이를 오류로 처리한다는 것이다

이를 처리하기 위해 취할 수 있는 방법은 여러가지가 있다

- 타입 단언

    ⇒ 타입스크립트에게 내가 맞으니 검사할 필요가 없다고 얘기해주는 것이다

    as 문법을 사용해 다음과 같이 넘겨주면 된다

    `let mySquare = createSquare({color: "black", triangle: true} as SquareConfig);`

- 문자열 인덱스 서명

    ⇒ 이 인터페이스에 추가적으로 프로퍼티가 들어올 수 있음을 알려주는 것이다

    ```tsx
    interface SquareConfig {
        color?: string;
        width?: number;
        [propName: string]: any;
    }
    ```

- 변수 할당

    ⇒ 다른 변수에 객체를 할당해서 프로퍼티로 넘겨주면 에러를 피할 수 있다 

    제일 처음 예시에서 보여준 코드 이걸 설명하지 않고 먼저 사용하면 당연히 의문을 가지게 될텐데..

    문서의 순서를 왜 이렇게 해놨는지 의문이 든다

# 함수 타입

인터페이스는 변수의 타입 뿐만 아니라 함수의 타입도 설정할 수 있다

```tsx
interface CombineStr {
    (str1 : string, str2: string) : string
}
```

문자열 두개를 받아서 문자열을 리턴하는 함수 타입의 형태를 지정한 인터페이스

를 이용해서 만든 함수이다

```tsx
const func : CombineStr = function (string:string, endChar:string){
    return string+endChar;
}
```

# 인덱서블 타입

사용자가 설정한 타입을 인덱스로 기술할 수 있는 방법

```tsx
interface IndexAbleType {
    [index: number]: any;
}
```

key를 숫자로 받고 value를 any로 받는 인터페이스, 배열과 유사한 형태를 띄는 객체로 사용할 수도 배열 자체를 사용할 수도 있다

```tsx
const array : IndexAbleType = [1,2,3,4];

const likeArr : IndexAbleType = {
    1: 'str',
    2: 'zzz',
};
```

# 클래스 타입

## 인터페이스 구현하기

다른 언어와 마찬가지로 클래스에 implements를 이용하면 클래스 내에서 재정의해야한다

```tsx
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date): void;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) {}
}
```

## 생성자 인터페이스

```tsx
class Dog {
    constructor(public name :string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}

class Cat {
    constructor(public name: string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}
```

이렇게 동일한 코드를 보면 묶어주고 싶은게 개발자의 심리인데 인터페이스를 사용해서 name과 age를 이용한 생성자 인터페이스를 만들어 줄 수 있다

```tsx
interface createDC {
		new (name: string, age: number);
}
```

앞에 new를 이용해서 생성자 인터페이스를 만들어 준 후에 implements로 구현사항이 강제되도록 해보면

```tsx
class Dog implements createDC{ // 에러
    constructor(public name :string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}

class Cat implements createDC{ // 에러
    constructor(public name: string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}
```

에러가 나는 것을 볼 수 있는데 

클래스가 인터페이스를 구현할 때는 인스턴스만 검사하고 constructor인 스태틱은 검사하지 않기 때문이다

그럼 어떻게 해야할까?

```tsx
interface createDC {
    new (name:string, age: number);
}

class Dog {
    constructor(public name :string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}

class Cat {
    constructor(public name: string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}

function createAnimal(creator : createDC, name : string, age: number) {
    return new creator(name, age);
}

```

이런 식으로 한 단계 돌아가서 반려동물 생성 함수를 만든 후에 파라미터로 들어오는 생성자를 인터페이스로 검사할 수 있다

다른 방법도 있다

```tsx
class Animal {
    constructor(public name: string, public age : number) {
        this.name = name;
        this.age  = age;
    }
}
interface Cry{
    cry : ()=>void;
}
const MakeDog : createDC = class Animal implements Cry{
    cry = ()=>{
        console.log('멍멍');
    }
}
const MakeCat : createDC = class Animal implements Cry{
    cry = ()=>{
        console.log('야옹');
    }
}

const dog1 = new MakeDog('댕댕이',1);
const cat1 = new MakeDog('고양이',1);
```

## 인터페이스 확장하기

인터페이스는 다른 인터페이스의 멤버를 복사하는 것이 가능하다

```tsx
interface Vehicle {
    wheel : number;
}

interface Motorcycle extends Vehicle{
    handle : string;
}

const MyCar = {} as Motorcycle;

MyCar.wheel = 1;
MyCar.handle = 'high';
```