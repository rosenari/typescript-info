#### 인터페이스

> 첫번째 인터페이스

```typescript
//해당 함수는 string 타입 label을 갖는 객체를 하나의 매개변수로 갖는다.
//컴파일러는 최소한 필요한 프로퍼티가 있는지와 타입이 맞는지 검사한다.
function printLabel(labeledObj: {label: string }){
    console.log(labeledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

```typescript
interface LabeledValue{
    label: string;
}

//인터페이스와 동일한 형태의 객체는 매개변수로 전달가능하다.
function printLabel(labeledObj: LabeledValue){
    console.log(labeledObj.label);
}

let myObj = {size:10, label:"Size 10 Object"};
printLabel(myObj); //가능
```

> 선택적 프로퍼티
> 인터페이스의 모든 프로퍼티가 필요한 것은 아님.
> 선택적 프로퍼티로 선언하면 꼭 매개변수가 해당 프로퍼티를 가지고 있지 않아도 된다

```typescript
interface SquareConfig{
    color?:string; //?를 붙여 선택적 프로퍼티로 선언
    width?:number; //?를 붙여 선택적 프로퍼티로 선언
}

function createSquare(config: SquareConfig): {color: string; area: number}{
    let newSquare = {color:"white",area:100}; //리턴 기본값
    if(config.color){ //매개변수에 color프로퍼티가 존재할 경우
        newSquare.color = config.color;
    }

    if(config.width){ //매개변수에 width프로퍼티가 존재할경우
        newSquare.area = config.width * config.width;
    }

    return newSquare;
}
```

> 읽기전용 프로퍼티 (Readonly) - 수정 불가능한 프로퍼티 선언(객체생성시에만 수정가능)

```typescript
interface Point{
    readonly x: number;
    readonly y: number;
}

let p1: Point = {x:10,y:20};
p1.x = 5; //오류
```

```typescript
let a: number[] = [1,2,3,4];
let ro: ReadonlyArray<number> = a;//ReadonlyArray<T>타입은 변경불가능한 배열
ro[0] = 12; //오류
ro.push(5); //오류
ro.length = 100; //오류
a = ro; //오류

a = ro as number[]; //타입단언으로 오버라이드하는 것은 가능
```

> 초과 프로퍼티 검사 (Excess Property Checks)

```typescript
interface SquareConfig{
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig) : { color: string; area: number} {
    //...
}

let mySquare = createSquare({colour:"red",width:100}); //에러
//Typescript는 이 코드에 버그가 있을 거라 생각한다.
//대상타입이 갖고 있지 않은 프로퍼티를 갖고 있기때문이다.
//* 타입스크립트는 다른변수에 할당할때나 인수로 전달할 경우 초과프로퍼티 검사를 수행한다.
```

```typescript
//초과프로퍼티 검사를 피하는 방법은 타입단언을 사용하면 된다.
let mySquare = createSquare({width:100,opacity:0.5} as SquareConfig);
```

```typescript
//다른 프로퍼티가 들어오는 것이 확인된다면, 문자열 인덱스 서명을 추가하는 것이 좋다.
interface SquareConfig{
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

```typescript
// 객체를 다른 변수에 할당할 경우에도 추가프로퍼티 검사를 받지 않는다.
let squareOptions = { colour:"red", width:100 };
let mySquare = createSquare(squareOptions); //성공
```

```typescript
let squareOptions = { colour: "red"};
let mySquare = createSquare(squareOptions); 
//squareOptions와 대상타입간의 공통프로퍼티가 없다면 에러가 난다.
```

> 함수 타입 (Function Types)

```typescript
interface SearchFunc{
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc; //함수타입의 변수생성
mySearch = function(source: string,subString: string){
    //매개변수의 이름이 같은필요는 없음
    let result = source.search(subString);
    return result > -1;
}
//같은 타입의 함수값으로 할당
```

```typescript
let mySearch: SearchFunc;
mySearch = function(src, sub){
    //함수 매개변수들은 같은 위치 대응되는 매개변수끼리 검사합니다.
    //타입스크립트의 문맥상 타이핑이 인수타입을 추론할 수 있습니다.
    let result = src.search(sub);
    return result > -1;//반환타입이 반환값으로 추론됨
}
```

> 인덱서블 타입(Indexable Types)

```typescript
//인덱서블 타입은 인덱싱할때 반환유형과 함게 객체를 인덱싱할때 사용할수있는
//타입을 기술하는 인덱스 시그니처를 가지고있다.
interface StringArray{
    [index: number]: string; //인덱스 시그니처
    //number로 색인화되면 string을 반환할 것을 나타냄
}

let myArray: StringArray;
myArray = ["Bob","Fred"];

let myStr: string = myArray[0];
```

> 인덱스 서명을 지원하는 타입은 문자열과 숫자이다.
> 두 타입의 인덱서를 모두 지원하는 것은 가능하나, 숫자 인덱서에서 반환된 타입은
> 반드시 문자열 인덱서에서 반환된 타입의 하위타입이어야한다.
>  [number로 인덱싱할때 실제로 string으로 변환되는데, 예를들어 100을 인덱싱했을때 "100"으로 인덱싱하는 것과같아서 Dog로 나올경우 Animal은 Dog의 상위타입으로 일관성유지가 되지않는다. ]

```typescript
class Animal{
    name: string;
}

class Dog extends Animal{
    breed: string;
}

interface NotOkay{
    [x: number]: Animal;
    [x: string]: Dog;
}
```

```typescript
//문자열 인덱스 시그니처는 모든 프로퍼티들이 반환타입과 일치하도록 강제한다.
interface NumberDictionary{
    [index: string]: number; //문자열 인덱서의 반환타입은 number
    length: number; //성공
    name: string; //오류 string타입은 인덱서반환타입(number)의 하위타입이아님
}
```

```typescript
interface NumberOrStringDictionary{
    [index: string]: number | string; 
    length: number; //성공 
    name: string; //성공
}
```

```typescript
//인덱스의 할당을 막기위해 인덱스시그니처를 읽기전용으로 만들수있다.
interface ReadonlyStringArray{
    readonly [index: number]: string;
}

let myArray: ReadonlyStringArray = ["Alice","Bob"];
myArray[2] = "Mallory"; //오류
```

> 클래스 타입 (Class Types)

```typescript
interface ClockInterface{
    currentTime: Date;
    setTime(d: Date): void;
}

//인터페이스 구현
class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date){
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

> 클래스의 스태틱과 인스턴스의 차이점
> 클래스는 두가지 타입을 갖는데, 생성자는 스태틱타입이다.
> 인터페이스 구현시 클래스의 인스턴스만 검사하기 때문에 생성자를 인터페이스에 기술한다면 에러가뜬다.
> 생성자를 인터페이스의 정의하는 방법은 다음과 같다.

```typescript
interface ClockConstructor{
    new (hour: number,minute: number);
}

class Clock implements ClockConstructor{
    currentTime: Date;
    constructor(h: number,m: number){ }
}
```

> 생성자를 매개변수로 전달하여 인스턴스를 생성하는 함수를 정의할수 있다.

```typescript
interface ClockConstructor{
    new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface{
    tick(): void;
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface{
    return new ctor(hour,minute);
}

class DigitalClock implements ClockInterface{
    constructor(h: number,m: number){ }
    tick(){
        console.log("beep beep");
    }
}

class AnalogClock implements ClockInterface{
    constructor(h: number, m: number){ }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock,12,17);
let analog = createClock(AnalogClock,7,32);
```

> 인터페이스 확장하기
> 인터페이스들도 확장(extend)이 가능합니다.

```typescript
interface Shape{
    color: string;
}

interface Square extends Shape{
    sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
```

> 여러 인터페이스를 확장할수 있습니다.

```typescript
interface Shape{
    color: string;
}

interface PenStroke{
    penWidth: number;
}

interface Square extends Shape,PenStroke{
    sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

> 하이브리드 타입
> 하이브리드타입은 여러 타입으로 동작하는 객체의 타입을 의미합니다.
> 예를 들어 함수와 객체 역할 모두가 가능한 객체타입을 생성할 수 있습니다.

```typescript
interface Counter{
    (string: number): string; //함수역할 정의
    interval: number;
    reset(): void;
}

function getCounter(): Counter{
    let counter = (function (start: number) { }) as Counter;
    counter.interval = 123;
    counter.reset = function() {};
    return counter;
}

let c = getCounter();
c(10); //함수로 동작가능
c.reset();//메서드 실행
c.interval = 5.0; //인스턴스변수
```

> 클래스를 확장한 인터페이스
> 인터페이스 타입이 클래스 타입을 확장하면 클래스 멤버는 상속받으나, 구현은 상속받지 않는다.
> 인터페이스는 private와 protected 멤버도 상속받는다.
> 허나 그 클래스나 하위 클래스에 의해서만 구현될수 있다.

```typescript
class Control{
    private state: any;
}

interface SelectableControl extends Control{
    select(): void;
}

// Control의 하위 클래스이므로 구현가능하다.
class Button extends Control implements SelectableControl{
    select() {}
}

class TextBox extends Control{
    select() {}
}

//Control의 하위 클래스가 아니므로 구현불가
class Image implements SelectableControl{
    private state: any;
    select() {}
}
```