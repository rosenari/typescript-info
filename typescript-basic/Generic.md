#### 제네릭

> 제네릭 Hello World

```typescript
function identity(arg: number): number{
    return arg;
}
//제네릭이 없다면 매개변수 및 값 리턴시 특정타입을 명시해야합니다.
```

```typescript
function identity(arg: any): any{
    return arg;
}
//아니면 any타입을 이용하여 기술할 수도 있습니다.
```

```typescript
function identity<T>(arg: T): T{
    return arg;
}
//'T'를 통해 매개변수 및 리턴타입을 명시할 수 있습니다.
//또한 타입추론을 통해 매개변수를 통해서도 타입을 캡처할 수 있습니다.
```

```typescript
let output = identity<string>("myString"); 
//출력타입은 string입니다.
//T를 string으로 지정하는 방법 1
```

```typescript
let output = identity("myString");
//T를 string으로 지정하는 방법 2 ( 인수 추론 방법 )
```

> length와 같은 멤버사용시 배열인수를 활용합니다.

```typescript
function loggingIdentity<T>(arg: T): T{
    console.log(arg.length);
    return arg;
}
//컴파일러 오류 : T에는 .length가 없습니다.
```

```typescript
function loggingIdentity<T>(arg: T[]): T[]{
    console.log(arg.length);
    return arg;
}
//정상 컴파일
```

```typescript
function loggingIdentity<T>(arg: Array<T>): Array<T>{
    console.log(arg.length);
    return arg;
}
//정상컴파일
```

> 제네릭 타입

```typescript
function identity<T>(arg: T): T{
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
//제네릭함수타입은 <T>(arg: T) => T입니다.
```

```typescript
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
//제네릭 타입매개변수를 다른이름으로 해도 됩니다.
```

```typescript
function identity<T>(arg: T): T{
    return arg;
}

let myIdentity: { <T>(arg: T): T } = identity;
//객체 리터럴 타입을 사용해도 됩니다.
```

```typescript
interface GenericIdentityFn<T> {
    (arg: T): T;
}
//인터페이스를 활용할 수 있습니다.


function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
//인터페이스 타입 활용
let myIdentity2: GenericIdentityFn<number> = identity;
//타입인수에 제약을 걸 수 있습니다.(number)
```

> 제네릭 제약조건

```typescript
function loggingIdentity<T>(arg: T): T{
    console.log(arg.length);
    return arg;
}
//오류 : 컴파일러는 T에 length가 있다는 것을 증명할 수 없음
```

> length 오류 해결방법으로 extends키워드를 활용할수 있습니다.

```typescript
interface Lengthwise{
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T{
    console.log(arg.length);
    return arg;
}
//기존에는 컴파일러단에서 에러가 났으나
//length 멤버를 가진 인터페이스와 extends 키워드를 활용해
//T에 length가 있다는 것을 명시하면 에러가 나지않습니다.
```

```typescript
loggingIdentity(3); //오류,length 프로퍼티가 없다.
```

```typescript
loggingIdentity({length:10,value:3}); //성공
```

> 제네릭 제약조건에서 타입 매개변수 사용

```typescript
//key of T는 T가 가진 프로퍼티이름들의 합집합
function getProperty<T,K extends keyof T>(obj: T,key: K){
    return obj[key];
}

let x = { a: 1,b: 2,c: 3,d: 4};

getProperty(x,'a');//성공
getProperty(x,'m');//오류 인수 타입 m은 a,b,c,d 중하나에 해당하지 않음.
```

> 제네릭에서 클래스 타입 사용

```typescript
function create<T>(c: {new(): T; }): T {
    return new c();
}
//생성자 타입을 받아 인스턴스 리턴
```

```typescript
class BeeKeeper{
    hasMask: boolean;
}

class ZooKeeper{
    nametag: string;
}

class Animal{
    numLegs: number;
}

class Bee extends Animal{
    keeper: BeeKeeper;
}

class Lion extends Animal{
    keeper: ZooKeeper;
}

function createInstance(A extends Animal)(c: new() => A): A {
    return new c();
}

createInstance(Lion).keeper.nametag; //타입검사
createInstance(Bee).keeper.hasMask; //타입검사
```