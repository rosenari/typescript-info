#### 함수

> 함수 (Function)
> 타입스크립트에 함수는 자바스크립트처럼 기명함수와 익명함수로 만들 수 있다.

```javascript
//자바스크립트 기명 함수
function add(x,y){

}

//자바스크립트 익명 함수
let myAdd = function(x, y) { return x + y };
```

```javascript
let z = 100;
function addToZ(x,y){
    return x + y + z; //외부 변수를 참조 할 수 있습니다.
}
```

> 함수 타입
> 함수 타이핑

```typescript
//파라미터의 타입과 리턴타입을 정해줄수있다.
function add(x: number,y: number): number{
    return x + y;
}

let myAdd = function(x: number,y: number): number { return x+y };
```

> 함수 타입 작성하기

```typescript
let myAdd: (x:number,y:number) => number =
    function(x:number,y:number): number {
        return x + y;
    };
// 함수의 타입은 매개변수의 타입과 반환타입이 있다.
// 매개변수명은 일치하지 않아도 된다.
```

> 타입 추론
> 함수타입에 매개변수와 반환타입이 명시된 경우 추론이 가능하다.

```typescript
let myAdd = function(x: number,y: number): number { return x + y };

let myAdd: (baseValue: number, increment: number) => number =
    function(x,y) { return x + y };
```

> 선택적 매개변수와 기본 매개변수
> javascript에서는 매개변수가 선택적이나 타입스크립트는 개수가 정확히 일치해야한다.
> 이런 경우안에서 매개변수를 선택적으로 받도록 지정할 수있다.

```typescript
function buildName(firstName: string, lastName: string){
    return firstName + " " + lastName;
}

let result1 = buildName("Bob"); //오류 너무 적은 매개변수
let result2 = buildName("Bob","Adams","Sr."); //오류 너무 많은 매개변수
let result3 = buildName("Bob","Adams"); //정확
```

```typescript
function buildName(firstName: string,lastName?: string){
    if(lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob"); //문제없음
let result2 = buildName("Bob","Adams","Sr."); // 오류 너무많은 매개변수
let result3 = buildName("Bob","Adams"); //정확
```

```typescript
//기본값 지정을 통해서도 undefined값이나, 전달되지않는 경우에 대해 대응할수 있다.
function buildName(firstName: string,lastName = "Smith"){
    return firstName + " " + lastName;
}

let result1 = buildName("Bob"); //Bob Smith 반환
let result2 = buildName("Bob",undefined); //Bob Smith 반환
let result3 = buildName("Bob","Adams","Sr"); //오류, 너무많은 매개변수
let result4 = buildName("Bob","Adams"); //Bob Adams 반환
```

```typescript
//선택적 매개변수는 앞에 먼저와도 상관없습니다.
function buildName(firstName = "Will", lastName: string){
   return firstName + " " + lastName; 
}

let result1 = buildName("Bob"); //오류, 너무적은 매개변수
let result2 = buildName("Bob","Adams","Sr.");//오류 너무많은 매개변수
let result3 = buildName("Bob","Adams"); //성공 Bob Adams 반환
let result4 = buildName(undefined,"Adams"); //성공 Will Adams반환
```

> 나머지 매개변수
> 많은 매개변수를 취할때, 갯수를 알수없을 때 나머지 매개변수를 활용할 수 있다.

```typescript
function buildName(firstName: string, ...restOfName: string[]){
    return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph","Samuel","Lucas","MacKinzie");
//Joseph Samuel Lucas MacKinzie반환
```

```typescript
//...부호는 나머지 매개변수가 있는 함수의 타입에도 사용된다.
function buildName(firstName: string, ...restOfName: string[]){
    return firstName + " " + restOfName.join(" ");
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```

> this와 화살표 함수
> 자바스크립트에서 this는 함수호출시 정해지는 변수이다.

```typescript
let deck = {
    suits: ["hearts","spades","clubs","diamonds"],
    cards: Array(52),
    createCardPicker: function(){
        return function(){
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit],card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
//함수호출시 this는 window가 된다. strict mode에서는 undefined
```

```typescript
//호출시 함수를 생성한 곳의 'this'를 캡처하고 싶다면, 화살표함수를 사용한다.
//this.suits[pickedSuit]의 this는 any타입니다.
//이경우 --noImplicitThis 옵션을 주고 컴파일러를 실행하면 오류를 반환
let deck = {
    suits: ["hearts","spades","clubs","diamonds"],
    cards: Array(52),
    createCardPicker: function(){
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit],card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
```

> this 매개변수
> this.suits[pickedSuit]의 this는 객체 리터럴 내부함수에서 왔기때문에 any타입니다. 
> 이것을 고치기 위해서 명시적 this 매개변수를 줄 수 있습니다.

```typescript
interface f(this: void){
    //독립형 함수에서 this를 사용할수 없도록 명시
}

interface Card{
    suit: string;
    card: number;
}

interface Deck{
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}

let deck: Deck = {
    suits:["hearts","spades","clubs","diamonds"];
    card: Array(52),
    //아래 함수는호출하는 객체가 반드시 Deck타입이어야한다는 것을 명시적으로 지정
    //--noImplicitThis 플래그로 컴파일러를 실행해도 
    //this가 any타입이 아니라서 오류 x
    createCardPicker: function(this: Deck){
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCrad / 13);

            return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: "+pickedCard.card+" of "+pickedCard.suit);
```

> 콜백에서 this 매개변수
> 라이브러리들은 콜백을 일반 함수처럼 호출하므로 this는 undefined가 된다.
> 이러한 문제를 사전방지하기위해 this매개변수를 활용할수 있다.

```typescript
interface UIElement{
    addClickListener(onclick: (this: void, e: Event) => void): void;
}

class Handler{
    info: string;
    onClickBad(this: Handler,e: Event){
        this.info = e.message;
    }
}

let h = new Handler();
uiElement.addClickListener(h.onClickBad);
//this를 사용하지않기때문에(this: void) 오류가난다. 
```

```typescript
class Handler{
    info: string;
    onClickBad(this: void,e: Event){
        //void 타입이므로 this는 이곳에서 사용못하게 방지
        console.log("clicked !!");
    }
}

let h = new Handler();
uiElement.addClickListener(h.onClickBad);
```

> 콜백에서 this를 사용하기 원한다면 화살표 함수를 사용해야한다.

```typescript
class Handler{
    info: string;
    //화살표함수 사용시 콜백에서 this 사용가능하다.
    onClickBad = (e: Event) => {this.info = e.message};
}
```

> 오버로드를 통해 매개변수타입검사를 수행할 수 있다.
> any를 반환하는 타입의 함수는 오버로드 목록에 해당되지 않는다.(검사 x)

```typescript
let suits = ["hearts","spades","clubs","diamonds"];

function pickCard(x: {suit:string; card: number;}[]): number;
function pickCard(x: number): {suit: string; card: number; };

//위의 두개 매개변수 타입 외에 다른타입으로 호출시 에러발생한다.
//사전 방지 
function pickCard(x): any{
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }

    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```