# 클래스

# 프로퍼티

타입스크립트는 자바스크립트와 다르게 클래스 내에서 선언한 프로퍼티들만 사용할 수 있다

```jsx
class Animal {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

이런 코드는 자바스크립트에선 아무런 문제 없이 돌아가지만 타입스크립트에서는 에러를 발생시킨다

```tsx
class Animal {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }
}
```

이렇게 클래스 내에서 선언해주어야 그 이후에 사용이 가능하다

# 상속

이미 존재하는 클래스를 이용해 상속 받아서 새로운 클래스를 만들 수 있다

```tsx
class Vehicle {
    name: string;
    price: number;

    constructor(name: string, price: number) {
        this.name = name;
        this.price = price;
    }
    move(distance: number = 100) {
        console.log(`${this.name} can move ${distance}m`);
    }
}
class Car extends Vehicle{
    constructor(name: string, price: number) {
        super(name,price);
    }
    sound(){
        console.log('부르릉');
    }
    move(distance: number = 200) {
        super.move(distance);
    }
}

const myCar = new Car('myCar',10);

myCar.sound();
myCar.move();
```

여기서 Car은 Vehicle이 가진 프로퍼티와 메서드를 모두 가진 인스턴스를 생성할 수 있다

그렇기 때문에 myCar에서 move를 그대로 상속받아서 사용할 수 있는 것

# 접근 제한자

자바스크립트에서는 기본적으로 프로퍼티에 대한 접근을 막을 수 있는 방법이 제공되지 않기 때문에 클로저를 이용해서 이를 구현했는데

타입스크립트에서는 다른 언어들과 같이 public, private, protect 접근 제한자를 제공해준다

## public

명시하지 않고 프로퍼티를 그냥 생성했을 경우 기본값인 public으로 설정된다

또 접근 제한자는 생성자 함수에서 다음과 같이 사용이 가능하다

```tsx
class Vehicle {
    constructor(public name: string,public price: number) {
        this.name = name;
        this.price = price;
    }
}
```

이렇게 생성자 함수에 접근 제한자와 같이 선언해주면 클래스 내부에서 프로퍼티가 자동으로 선언된다

## private

private 프로퍼티는 프로퍼티를 선언한 그 클래스 내부에서만 접근이 가능하고 다른 외부에서 접근할 수 없다

```tsx
class Vehicle {
    constructor(public name: string,public price: number, private model: number){
        this.name = name;
        this.price = price;
        this.model = model;
    }
    inVehicle(){
        console.log(this.model); // 접근 가능
    }
    move(distance: number = 100) {
        console.log(`${this.name} can move ${distance}m`);
    }
}
class Car extends Vehicle{
    constructor(name: string, price: number,model: number= 1000) {
        super(name,price,model);
    }
    sound(){
        console.log('부르릉');
    }
    move(distance: number = 200) {
        super.move(distance);
    }
    // inCar(){
    //     console.log(this.model); 에러
    // }
}

const myCar = new Car('myCar',10);
```

## protect

public보단 폐쇠적이지만 private보단 덜 폐쇠적인 접근 제한자이다 처음 선언한 클래스에서 사용은 물론 그 클래스를 상속받는 모든 클래스 또한 접근이 가능하다

```tsx
class Vehicle {
    constructor(public name: string,public price: number, protected model: number) {
        this.name = name;
        this.price = price;
        this.model = model;
    }
    inVehicle(){
        console.log(this.model); // 접근가능
    }
    move(distance: number = 100) {
        console.log(`${this.name} can move ${distance}m`);
    }
}
class Car extends Vehicle{
    constructor(name: string, price: number,model: number= 1000) {
        super(name,price,model);
    }
    sound(){
        console.log('부르릉');
    }
    move(distance: number = 200) {
        super.move(distance);
    }
    inCar(){
        console.log(this.model); // 접근 가능
    }
}

const myCar = new Car('myCar',10);
```

## readOnly

얘는 말 그대로 읽기 전용인 프로퍼티를 선언할 때 쓰는 제한자로 직접 수정이 불가능하고 읽기만 가능하다

# getter, setter

프로퍼티를 private로 설정해두면 읽기도 안되고 수정도 안되는데 대체 왜 사용하는걸까 했는데

private로 설정한 프로퍼티는 클래스 내부에서 

프로퍼티를 읽을 수 있는 getter와 

프로퍼티를 수정할 수 있는 setter 메서드를 만들어 주어야 한다 

```tsx
class Vehicle {
    constructor(public name: string,public price: number, private _model: number) {
        this.name = name;
        this.price = price;
        this._model = _model;
    }
    get model(): number {
        return this._model;
    }

    set model(value: number) {
        this._model = value;
    }
    move(distance: number = 100) {
        console.log(`${this.name} can move ${distance}m`);
    }
}
```

다시금 모델 프로퍼티를 private으로 설정 한 후 내부에서만 사용되는 변수들은 _를 붙여주는 컨벤션을 지키고 나서 get과 set을 만들어 주었다

 

# 전역 프로퍼티

전역 프로퍼티는 클래스에서 만들어진 인스턴스에서 사용할 수 있는 프로퍼티가 아닌 클래스 그 자체에서 사용할 수 있는 프로퍼티를 말한다

```typescript
class Vehicle {
    static description :string = 'A vehicle is a machine that transports people or cargo';

    constructor(public name: string,public price: number, private _model: number) {
        this.name = name;
        this.price = price;
        this._model = _model;
    }
    get model(): number {
        return this._model;
    }

    set model(value: number) {
        this._model = value;
    }
    move(distance: number = 100) {
        console.log(`${this.name} can move ${distance}m`);
    }

    static staticMethod (){
        console.log('this is static method');
    }
}

const test = new Vehicle('test1',1000,1);

console.log(test.description); // 접근 불가
test.staticMethod(); // 접근 불가
console.log(Vehicle.description); // A vehicle is a machine that transports people or cargo
Vehicle.staticMethod(); // this is static method
```