#### 타입스크립트 기초

> 불리언 (Boolean)

```typescript
let isDone: boolean = false;
```

> 숫자 (Number)

```typescript
let decimal: number = 6; //십진수
let hex: number = 0xf00d; //16진수
let binary: number = 0b1010; //2진수
let octal: number = 0o744; //8진수
```

> 문자열 (String)

```typescript
let color: string = "blue";
color = 'red';
```

```typescript
let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ fullName }.
I'll be ${ age + 1 } years old next month.`;
```

```typescript
let sentence: string = "Hello, my name is " + fullName + ".\n\n" +
"I'll be " + (age + 1) + " years old next month.";
```

> 배열 (Array)

```typescript
let list: number[] = [1,2,3];
let list: Array<number> = [1,2,3];
```

> 튜플 (Tuple)

```typescript
let x: [string,number]; //선언

x = ["hello",10]; //성공
x = [10,"hello"]; //오류

console.log(x[0].substring(1)); //성공
console.log(x[1].substring(1)); //실패 - number는 substring이 없다.

x[3] = "world"; //프로퍼티 3이 없음.
```

> 열거 (Enum)

```typescript
enum Color {Red,Green,Blue};

let c: Color = Color.Green;
```

```typescript
enum Color {Red=1,Green,Blue}; //값을 1부터 시작함. Red=1,Green=2,Blue=3

let c: Color = Color.Green;
```

```typescript
enum Color {Red=1,Green=2,Blue=4}; //값을 수동 설정 가능
```

```typescript
enum Color {Red=1,Green,Blue};

let colorName: string = Color[2]; //1은 Red, 2는 Green

console.log(colorName); //값이 2인 Green이 출력됨
```

> Any (알지 못하는 타입 - 모든 타입가능)

```typescript
let notSure: any = 4;
notSure = "maybe a string instead"; //성공
notSure = false; //성공
```

```typescript
let notSure: any = 4;
notSure.toFixed(); //성공 런타임에 존재 - 컴파일러는 검사안함

let prettySure: Object = 4;
prettySure.toFixed(); //오류 : toFixed는 Object에 존재하지않음.
```

```typescript
let list: any[] = [1,true,"free"]; //any타입은 여러타입이섞인 배열사용에 유용

list[1] = 100;
```

> Void (어떤타입도 존재하지 않음)

```typescript
function warnUser(): void{
    console.log("this is my warning message");
    //void는 보통 함수에서 반환값이 없을때 사용
}
```

```typescript
let unusable: void = undefined;
unusable = null; //성공 - void 타입은 null과 undefined만 할당 가능하다.
```

> Null and Undefined

```typescript
let u: undefined = undefined;
let n: null = null;
//undefined와 null은 해당 타입만 할당 가능합니다 !
//기본적으로 null과 undefined는 모든 타입의 하위타입이므로, number와 같은
//타입에 할당가능합니다.
```

> Never (항상 오류를 발생하거나 절대반환하지 않는 반환타입)

```typescript
//never를 반환하는 함수는 함수의 마지막에 도달할 수 없다.
function error(message: string): never{
    throw new Error(message);
}

function fail(){
    return error("something failed");
}

function infiniteLoop(): never{
    while(true){ }
}
```

> 객체 (Object)

```typescript
//object는 원시타입이 아닌 타입
declare function create(o: object | null): void;

create({prop:0}); //성공
create(null); //성공

create(42); //오류
create("string"); //오류
create(false); //오류
create(undefined); //오류
```

> 타입 단언 (다른 언어의 형변환과 유사)

```typescript
//타입단언은 컴파일러에게 타입을 명시하여 알려줌.
let somValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
//컴파일러에게 이건 string이야 날 믿어라고 말함
```

```typescript
//as문법을 통해서도 타입단언이 가능
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```