# 타입스크립트


## 왜 타입스크립트를 사용해야할까?

- 자바스크립트는 동적 언어이다

    ⇒ 컴파일 시점에 에러를 잡지 않고 런타임 환경에서 에러가 쉽게 발생할 수 있는 단점을 가진다

- 객체 지향 프로그래밍이 가능하다

    ⇒ 인터페이스, 제네릭타입 등 으로 강력한 객체 지향 프로그래밍이 가능하다

- 자바스크립트의 기능들을 그대로 제공한다


# 기본 타입

## 기존 타입

원래 자바스크립트에서 자주 사용되는 원시 타입의 변수와 객체

```tsx
// boolean
let bool : boolean = false;

// Number

let num : number = 1;

// String

let str : string = 'string';
let template : string = `this is ${str}`;

// Array

let arr : number[] = [1,2,3,4];
let arr1 : Array<string> = ['1','2','3','4'];
```

그냥 훑으면 이해되는 수준이다

변수를 선언하기 전 변수 명 뒤에 콜론을 붙여 그 변수의 타입을 명시적으로 할당해준다

차후 그 변수에 할당해준 타입이 아닌 다른 변수가 들어오게 되면 에러가 발생한다

### Null and Undefined

이 둘도 타입으로 지정이 가능하다

```tsx
// undefined null
let _undefined: undefined = undefined;
let _null: null = null;
```

이 둘은 각각 undefined null 외에 아무 값도 받지 못한다

기본적으로 null과 undefined는 서로 모든 타입의 하위 타입이다

⇒ number나 string, boolean으로 지정한 타입들에 null이나 undefined가 들어갈 수 있다는 얘기

```tsx
let str : string = 'string';
str = null; // 가능

let num : number = 1234;
num = undefined; // 가능
```

### Object

object는 원시 타입이 아닌 다른 타입들을 받을 수 있다

```tsx
let obj : object = {1:2,a:3};

obj = Array(1);
```

## 새 타입

새로운 타입이라고 해서 새로 생겨난 데이터 타입이 아니다 원래 자바스크립트의 객체를 응용해서 쓰는 것인데

그냥 자바스크립트만을 사용할 때 보지 못했던 낯선 타입들을 내 마음대로 나누어 보았다 

### Tuple

```tsx
// Tuple

let tuple : [string, number]; // 인덱스 0엔 문자열, 1엔 숫자가 들어가는 고정 배열이 생성됨

tuple = ['string', 1]; // OK
tuple = [1, 'string'] //정의된 타입의 순서가 바뀌어도 에러가 난다
console.log(tuple[3]); // 인덱스의 범위를 벗어났기 때문에 undefined이 아니라 컴파일 에러가 발생
```

언뜻 보면 그냥 배열과 다를 것이 없어 보이지만 정해진 순서에 정해진 타입값이 들어가야하는  배열

심지어 튜플에는 인덱스 범위 외에 접근하는 것이 허용되지 않는다

### Enum

다른 언어에서 볼 수 있는 그 열거형이 맞다

```tsx
// enum

enum names {park, kim, jung, lee}

console.log(names);
// {0: "park", 1: "kim", 2: "jung", 3: "lee", park: 0, kim: 1, jung: 2, lee: 3}
// index와 value, value와 index 쌍을 가진 객체가 생성된다

enum sort {one=4, two=8, three, four}

console.log(sort);
// {4: "one", 8: "two", 9: "three", 10: "four", one: 4, two: 8, three: 9, four: 10}
// index를 직접 지정할 수 있으며 지정한 다음 value는 +1씩 늘어난다t
```

### Any

any는 어떤 것이 들어올지 알 수 없을 때 어떠한 값이 들어오더라도 받을 수 있는 타입이다

따라서 any를 타입으로 지정하면 컴파일시에 강력한 타입 검사를 하지 않고 자바스크립트에서 작업할 때와 동일한 환경을 가질 수 있다 

```tsx
// any
let anything : any = 1;

anything = 'str';
anything = [];
```

### Void

any가 어떠한 값도 받을 수 있었다면 반대로 void는 어떠한 값도 받지 않음을 나타낸다

하지만 위에서 언급한 것 처럼 null과 undefined는 모든 타입의 하위 타입이기 때문에 할당이 가능하다

```tsx
// Void

let empty : void = undefined;

empty = null;
```

받을 수 있는 값이 undefined와 null밖에 없는데 굳이 사용해야할까..?

함수에는 유용하게 사용이 가능하다

```tsx
function voidFunction() : void {
    console.log('no return');
}
```

처럼 함수에 반환값이 없을 때 void타입을 지정해주면 된다

### Never

never은 절대 발생할 수 없는 타입을 나타낸다

문서를 보면

> 함수 표현식이나 화살표 함수 표현식에서 항상 오류를 발생시키거나 절대 반환하지 않는 반환 타입으로 쓰입니다

라고 한다

예제를 보면 아래와 같이 사용하면서 함수의 마지막에 도달할 수 없음을 강조하고 있다

즉, 함수가 정상적으로 종료되어서 무언가 반환하기 전에 에러로 끝나거나 혹은 끝나지 않거나.

```tsx
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {
    }
}
```

근데 이런 사용법은 void로 대체가 가능한데 굳이 never가 만들어진 이유가 있을까?

## Type assertions

타입 단언(Type assertions)은 들어올 변수가 어떤 타입인지 이미 알고 있을 경우 컴파일러에게 개발자가 미리 일러두는 것을 말한다

두 가지 방법이 있는데

- angle-bracket

    ```tsx
    let someValue: any = "this is a string";

    let strLength: number = (<string>someValue).length;
    ```

- as

    ```tsx
    let someValue: any = "this is a string";

    let strLength: number = (someValue as string).length;
    ```