# TypeScript
- ts는 js와 거의 동일한 데이터 타입 지원
## DataTypes
```typescript
let isDone: boolean = false; //Boolean
let num: number = 5; //Number
let color: string = "blue"; //String
let list: number[] = [1,2,3]; //Array
let list: Array<number> = [1,2,3]; // Generic Type Array
let tup: [string, number]; // Tuple 요소의 타입과 개수가 고정된 배열

enum Color {Red = 1, Blue, Green} //Default = 0
let color: Color = Color.GREEN; //enum

let notSure: any = 4;
notSure = true;
notSure = "maybe String"; //Any 알지못하는 Type을 표현해야 하는 경우

function error(message: string): never {
    throw new Error(message);
} // Never 절대 발생할 수 없는 Type / 항상 오류를 발생시키는 타입

declare function create(o: object | null): void; //Object 원시타입이 아닌 type
create(43) // 오류
create({props: 123}); //성공

let someValue: any = "this is string";
let strLength: number = (<string>someValue).length;
let strLength2: number = (someValue as string).length; //Type Assertion
```

## Type Annotations
- 함수나 변수에 특정 Type을 명명할 수 있음
```typescript
function greeter(name: string){
    return "Hello, " + name;
}

let name = "Jane";
document.body.textContent = greeter(name);
```

## Interface
- 명시적인 `implements` 없이, 인터페이스가 요구하는 형태를 사용하는 것만으로 Interface를 `Implement`할 수 있다.
- Duck Typing(=structural subtyping) : Type check가 값의 `형태에 초점을 맞추고 있다는 것.`(ts의 핵심원칙 중 하나)
- Interface는 이런 타입들의 `이름을 짓는 역할`을 하며, 코드 내,외부에서 사용하는 `코드의 Contract를 정의 하는 방법`
```typescript
function printLabelObjUsed(labeledObj: { label: string }){ //객체가 size라는 Property도 가지지만, label과 맞는지만 검사
    console.log(labeledObj.label);
}
interface LabeledValue {
    label: string;
}

function printLabelInterfaceUsed(labeledObj: LabeledValue){
    console.log(labeledObj.label);
}

let myObj = {size: 10, label: "Size 10"};
printLabelObjUsed(myObj)
printLabelInterfaceUsed(myObj); 

//Optional Properties
enum Color {Red, Blue, Green}
interface Square {
  color?: Color;
  width?: number;
}


function createSquare(config: Square): { color: Color; area: number } {
  let newSquare = {color: Color.Red, area: 100}; //default
  if (config.color) newSquare.color = config.color;
  if (config.width) newSquare.area = config.width * config.width; //인자가 존재할 때엔 이걸로 치환
  return newSquare;
}

let mySquare = createSquare({color: Color.Green}); // Green Colored, area = 100

//ReadOnly Property
interface Point {
    readonly x: number; //- readonly vs const : variable에는 const, property에는 readonly
    readonly y: number;
}
interface Line {
    pointList: ReadonlyArray<Point>; //ReadOnly Array
}
let p1: Point = {x: 10, y: 20};
p1.x = 15; // Error
```
### Function Types
- Interface는 함수 Type을 설명할 수 있다.
- Interface에 Call Signiture를 전달
```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string){ //parameter의 이름이 같을 필요는 없다.
    let result = source.search(subString);
    return result > -1;
}
```
### Indexable Type
- arr[10] / ageMap['myname'] 처럼 Type을 Index로 기술할 수 있다.
- 인덱싱 할 때 해당 Return Type과 함께 객체를 Indexing할 수 있는 Type을 기술하는 Index Signature를 가지고 있음
```typescript
interface StringArray {
    [index: number]: string;
} // StringArray가 number로 Indexing되면, String을 반환한다.

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
### Class Type
- Java에서 처럼 Interface > Implements가능
- Class가 Interface를 Implements할 때, Class의 인스턴스만 검사하기 때문에, 생성자가 Static이면 이 검사에 포함되지 않음
```typescript
interface Clock {
    currentTime: Date;
}

class DigitalClock implements Clock{
    currentTime: Date = new Date();
    constructor(h: number, m: number) { }
}
```
### Interface vs Type Alias
#### 공통점
- 타입에 대한 이름을 명명할 수 있다.
```typescript
interface Human{
    name: string;
    age: number;
}

type Human = {
    name: string;
    age: number;
}
```
- 여러 Type에 대한 관계 정의(extends, implements, Intersection Type, Union Type)
#### 차이점
- Interface는 `Declaration Merging이 가능`하지만, type은 불가능 -> OCP에 따라 extends에 열려있는 js 객체의 동작방식과 비슷하게 연결하도록 설계됨
- 따라서 Interface의 사용을 권고, type alias는 Union Type 또는 Tuple Type을 반드시 써야하는 상황에서 사용
##### Declaration Merging
- 컴파일러가 같은 이름으로 선언된 두 개의 개별적 선언을 하나로 합치는 것
```typescript
interface Box{
    height: number;
    width: number;
}

interface Box{
    scale: number;
}
let box: Box = {height: 5, width: 6, scale: 10};
```

## Class
- Class 기반 객체 지향 프로그래밍 지원
```typescript
class Student {
    fullName: string;
    constructor(public firstName: string, public middleInitial: string, public lastName: string){
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: String;
    lastName: string;
}

function greeter(person: Person){
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = new Student("Jane", "M.", "User");

document.body.textContent = greeter(user);
```

## Callback Function
- 어떤 Event가 발생한 후 수행될 함수
- js에서 function은 `1급 객체`이기 때문에 사용이 가능하다.
