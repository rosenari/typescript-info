#### 클래스

> 클래스 (Classes)

```typescript
class Greeter{
    greeting: string;
    constructor(message: string){
        this.greeting = message;
    }

    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

> 상속 (Inheritance)
> 상속은 이미 존재하는 클래스를 확장하여 새로운 클래스를 만들 수 있다.

```typescript
//기초 클래스,상위클래스
class Animal{
    move(distanceInMeters: number = 0){
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}

//파생클랫,하위클래스
class Dog extends Animal{
    bark(){
        console.log('Woof! Woof!');
    }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

```typescript
class Animal{
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0){
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal{
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5){
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal{
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45){
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
//Slithering...
//Sammy the Python moved 5m.
//Galloping...
//Tommy the Palomino moved 34m.
```

> public private protected
> 타입스크립트에서는 접근제어자를 선언하지않으면 기본적으로 public이다.

```typescript
class Animal{
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number){
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

> private의 경우 javascript에서는 #으로 사용하지만
> typescript에서는 private 키워드를 사용한다.

```typescript
class Animal{
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; //오류 name은 비공개이므로 class내에서만 접그가능
```

> 같은 형태의 클래스라도 private 멤버를 공유하지 않는다면 호환되지 않는다.

```typescript
class Animal{
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal{
    constructor() { super("Rhino"); }
}

class Employee{
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino; //rhino는 Animal의 하위클래스로 생성된 객체로 
//private 선언부가 동일하기때문에 호환가능
animal = employee; //오류, Animal과 Employee는 형태는 같으나 
//private 멤버의 선언부가 다르므로 호환 불가
```

> protected 이해하기
> protected는 파생된 클래스에서 접근가능하다.

```typescript
class Person{
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person{
    private department: string;

    constructor(name: string, department: string){
        super(name);
        this.department = department;
    }

    public getElevatorPitch(){
        return `Hello, my name is ${this.name} and I work in ${this.department}`
    }
}

let howard = new Employee("Howard","Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); //오류 protected는 외부에서 접근불가
```

> 생성자에도 protected를 부여할수 있으며, 외부에서 인스턴스를 생성할수없다.

```typescript
class Person{
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}

//Employee는 Person을 확장할 수 있습니다.
class Employee extends Person{
    private department: string;
    
    constructor(name: string, department: string){
        super(name);
        this.department = department;
    }

    public getElevatorPitch(){
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard","Sales");
let john = new Person("John"); //오류, Person의 생성자는 protected입니다.
```

> 읽기전용 지정자 (readonly)

```typescript
class Octopus{
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor(theName: string) {
        this.name = theName;
    }
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit";//오류, name은 읽기 전용.
```

> readonly 파라미터를 이용하면 멤버생성과 초기화를 한곳에서 할 수 있다.

```typescript
class Octopus{
    readonly numberOfLegs:number = 8;
    constructor(readonly name: string) {//선언과 동시에 할당
    }
    //접근제어자도 사용가능하다. public protected private
}
```

> 접근자 (Accessors)
> class에 getter,setter 를 설정할 수 있습니다.

```typescript
const fullNameMaxLength = 10;

class Employee{
    private _fullName: string;

    get fullName(): string{
        return this._fullName;
    }

    set fullName(newName: string){
        if(newName && newName.length > fullNameMaxLength){
            throw new Error("fullName has a max length of "+fullNameMaxLength);
        }

        this._fullName = newName;
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";//setter 호출
if(employee.fullName){
    console.log(employee.fullName);//getter 호출
}
```

> 전역 프로퍼티 ( static properties )
> static 멤버는 클래스 자체에 부여하는 값으로 클래스이름을 붙여 참조가능합니다.

```typescript
class Grid{
    static origin = {x:0,y:0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}){
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor(public scale: number) { }
}

let grid1 = new Grid(1.0); // 1x scale
let grid2 = new Grid(5.0); // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x:10,y:10}));
console.log(grid2.calculateDistanceFromOrigin({x:10,y:10}));
```

> 추상클래스 (Abstract Classes) - 상속강요
> 추상 클래스는 직접 인스턴스화 할수 없고, 파생된 클래스에서 가능합니다.
> 인터페이스와 달리 구현부를 정의할 수 있습니다.
> 선택적 접근지정자 포함 가능

```typescript
abstract class Department{
    constructor(public name: string){
    }

    printName(): void{
        console.log("Department name:" + this.name);
    }

    abstract printMeeting(): void; //파생 클래스에서 구현해야함
}

class AccountingDepartment extends Department{
    constructor(){
        super("Accounting and Auditing"); //파생클래스의 생성자는 반드시 super를 호출해야함
    }    

    printMeeting(): void{
        console.log("The Accounting Department meets each Monday at 10am.");
    }

    generateReports(): void{
        console.log("Generating accounting reports...");
    }
}

let department: Department; //추상타입의 레퍼런스 정의
department = new Department(); //오류, 추상클래스는 인스턴스화 불가능
department = new AccountingDepartment();//가능
department.printName();
department.printMeeting();
department.generateReports(); //오류, 추상타입에 메서드가 존재하지않음
```

> 클래스 자체타입
> typeof를 사용하여 인스턴스타입이 아닌 클래스 자체타입(생성자 함수타입)
> 을 선언하여 static 필드를 수정하여 새로운 생성자 함수를 생성할 수 있다.

```typescript
class Greeter{
    static standardGreeting = "Hello, there";
    greeting: string;
    greet(){
        if(this.greeting){
            return "Hello, " + this.greeting;
        }else{
            return Greeter.standardGreeting;
        }
    }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet()); //Hello, there

let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there !";

let greeter2: Greeter = new greeterMaker();
console.log(greeter2.greet()); //Hey, there !
```

> 인터페이스로써 클래스 사용하기
> 클래스는 인스턴스 타입과 생성자함수라는 타입 두가지를 생성한다.
> 고로 인터페이스의 상위타입으로 존재할 수 있다.

```typescript
class Point{
    x: number;
    y: number;
}

interface Point3d extends Point{
    z: number;
}

let point3d: Point3d = {x: 1, y: 2,z: 3};
```