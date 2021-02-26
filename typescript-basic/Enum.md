#### 열거형 - 이름이 있는 상수들의 집합

> 숫자 열거형

```typescript
enum Direction {
    Up = 1,
    Down, //2
    Left, //3
    Rright, //4
}
```

```typescript
enum Direction {
    Up, //0
    Down, //1
    Left, //2
    Right, //3
}
```

> 열거형 자체에서 프로퍼티로 모든 멤버에 접근가능합니다.
> 또한 열거형이름을 사용해 타입을 선언합니다.

```typescript
enum Response{
    No = 0,
    Yes = 1,
}

function respond(recipient: string,message: Response): void {
    //...
}

respond("Princess Caroline",Response.Yes);
```

> 계산된 멤버와 상수멤버를 섞어 사용할 수 있습니다.
> 다만 초기화되지 않은 열거형은 먼저 나와야합니다.

```typescript
enum E{
    A = getSomeValue(),
    B. // 초기화를 하거나 초기화 되지않은 멤버는 앞으로 가야함
}
```

> 문자열 열거형
> 유의미한 값을 부여하여 디버깅에 용이하다

```typescript
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

> 이종 열거형
> 열거형은 숫자와 문자를 섞어 사용가능하다. 그러나 그렇게하는 경우는 드물다.

```typescript
enum BooleanLikeHeterogeneousEnum{
    No = 0,
    Yes = "YES",
}
```

> 리터럴 열거형 멤버
> 상수 열거형 멤버는 아래의 값들로 초기화되는 멤버입니다.
  
- 문자 리터럴
- 숫자 리터럴
- 숫자 리터럴에 단항 연산자가 적용된 경우

> 열거형의 모든 멤버가 리터럴 열거형 값을 가지면 특별한 의미로 사용된다.
> 첫번째로 열거형 멤버를 타입처럼 사용가능하다.

```typescript
enum ShapeKind{
    Circle,
    Square,
}

interface Circle{
    kind: ShapeKind.Circle;
    radius: number;
}

interface Square {
    kind: ShapeKind.Square;
    sideLength: number;
}

let c: Circle = {
    kind: ShapeKind.Square, //오류, 타입 불일치
    radius: 100,    
}
```

> 런타임에서 열거형
> 열거형은 런타임에 존재하는 실제객체이므로, 함수의 매개변수로 전달할 수 있다.

```typescript
enum E {
    X,Y,Z
}

function f(obj: {X: number}){
    return obj.X;
}

f(E); //E가 X라는 숫자 프로퍼티를 가지므로 가능
```

> 컴파일 시점에서 열거형
> keyof typeof는 모든 열거형의 키를 문자열로 나타내는 타입(유니온)을 가져옵니다.

```typescript
enum LogLevel{
    ERROR,WARN,INFO,DEBUG
}

type LogLevelStrings = keyof typeof LogLevel;
/*
  위는 다음과 동일합니다.
  type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
*/

function printImportant(key: LogLevelStrings,message: string) {
    const num = LogLevel[key];
    if(num <= LogLevel.WARN){
        console.log('Log level key is: ',key);
        console.log('Log level value is: ',num);
        console.log('Log level message is: ',message);
    }
}

printImportant('ERROR','This is a message');
```

> 역 매핑
> 숫자 열거형 멤버는 열거형 값에서 열거형 이름으로 역매핑을 받습니다.

```typescript
enum Enum{
    A
}

let a = Enum.A;
let numOfA = Enum[a]; //"A"
```

```javascript
//위의 타입스크립트 코드는 다음과 같이 변환됩니다
var Enum;
(function (Enum){
    Enum[Enum["A"]=0] = "A";
})(Enum || (Enum = {}));
var a = Enum.A;
var nameOfA = Enum[a]; //"A"
```

> const 열거형 - 상수 열거형 표현식만 사용가능
> const 열거형은 컴파일 과정에서 완전히 제거되며,
> 사용하는 공간에 인라인 됩니다.

```typescript
const enum Directions{
    Up,
    Down,
    Left,
    Right,
}

let directions = [Directions.Up,Directions.Down,Directions.Left,Directions.Right]
```

```javascript
//위 코드는 다음과 같이 컴파일됩니다.
var directions = [0,1,2,3];
```

> Ambient 열거형
> 해당 열거형은 이미 존재하는 열거형 타입의 못브을 묘사하기위해 사용됩니다.
> 일반 열거형에서는 가장 첫번째 멤버가 상수일경우 그 다음도 상수로 간주됩니다.
> 허나 Ambient열거형에서는 초기화되지 않은 멤버는 반드시 계산된 멤버 간주합니다.

```typescript
declare enum Enum{
    A = 1,
    B, //계산된 멤버로 간주
    C = 2,
}
```