#### 하지말아야 할것과 해야할 것

> Number, String, Boolean, Symbol, Object 타입을 사용하지마세요.
> number, string, boolean, symbol 타입을 사용하세요.

```typescript
function reverse(s: String): String;
//잘못된 사용법

function reverse(s: string): string;
//올바른 사용법
```

> 콜백 반환 타입에 any를 사용하지마세요.
> void를 사용하세요.

```typescript
function fn(x: () => any){
    x();
}
//잘못된 사용법

function fn(x: () => void){
    x();
}
//올바른 사용법
```

> 콜백에 선택적 매개변수사용을 자제하세요.
> 비선택적인 매개변수 사용이 권장됩니다.

```typescript
interface Fetcher{
    getObject(done: (data: any, elapsedTime?: number) => void): void;
}
//잘못된 사용법

interface Fetcher{
    getObject(done: (data: any, elapsedTime: number) => void): void;
}
//올바른 사용법
```

> 콜백의 인수가 다른경우 오버로드보다는 최대인수를 사용하는 것을 권장합니다.
> 어짜피 콜백의 매개변수를 무시하는 것은 항상 허용되므로, 짧은 매개변수의 오버로드가 필요없습니다.

```typescript
declare function beforeAll(action: () => void, timeout?: number): void;
//인수가 없는 콜백
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
//인수가 있는 콜백
//위처럼 두개로 나누는 것은 권장되지 않습니다.

declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
//다음처럼 콜백의 인수를 최대로 놓으면 어짜피 인수를 넣지않아도 허용됩니다.
```

> 함수 오버로드시 구체적인 오버로드가 앞에 위치하도록 하세요.

```typescript
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;
//구체적인 것이 뒤에 위치하면 안됩니다.

var myElem: HTMLDivElement;
var x = fn(myElem); //any

//***************************************
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;
//다음처럼 구체적인 것이 앞에 위치해야합니다.

var myElem: HTMLDivElement;
var x = fn(myElem); //string
```

> 함수의 매개변수만 다른 경우 선택적 매개변수를 사용하세요.

```typescript
interface Example{
    diff(one: string): number;
    diff(one: string, two: string): number;
    diff(one: string, two: string, three: boolean): number;
}
//위처럼 매개변수만 다른경우는 오버로드로 처리하지마세요.

interface Example{
    diff(one: string,two?: string,three?: boolean): number;
}
//다음 처럼 선택적 매개변수를 활용하세요.
```

> 한 인수위치에서 타입만 다른 경우는 유니언 타입을 사용하세요.

```typescript
interface Moment{
    utcOffset(): number;
    utcOffset(b: number): Moment;
    utcOffset(b: string): Moment;
}
//동일한 인수위치에서 타입만 다른경우 위처럼 오버로드로 처리하지마세요.

interface Moment{
    utcOffset(): number;
    utcOffset(b: number|string): Moment;
}
//위처럼 유니언을 사용하세요.
```