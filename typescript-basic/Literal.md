#### 리터럴

> 리터럴 타입 좁히기

```typescript
//Typescript는 문자열이 아닌 "Hello World"로 타입을 정함
//이 객체는 절대 변경되지 않음을 알림
const helloworld = "Hello World";

//let 은 변경될 수 있으므로 컴파일러는 문자열이라고 선언함
let hiWorld = "Hi World";
```

> 문자열 리터럴 타입
> 문자열 리터럴 타입은 유니언타입,타입 가드 그리고 타입별칭과 잘 결합됩니다.
> 이런 기능을 사용해 문자열로 enum과 비슷한 형태를 갖출 수 있습니다.

```typescript
type Easing = "ease-in" | "ease-out" | "ease-in-out";

class UIElement{
    animate(dx: number,dy: number, easing: Easing){
        if(easing === "ease-in"){
            //...
        }else if(easing === "ease-out"){

        }else if(easing === "ease-in-out"){

        }else{

        }
    }
}

let button = new UIElement();
button.animate(0,0,"ease-in");
button.animate(0,0,"uneasy"); //error 
//uneasy타입은 "ease-in" | "ease-out" | "ease-in-out" 타입의
//매개변수에 할당할수 없다.
```

```typescript
//문자열 리터럴 타입은 오버로드를 구별하는 것과 동일한 방법으로 사용가능
function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
function createElement(tagName: string): Element{
    //..로직 추가
}
```

> 숫자형 리터럴 타입

```typescript
//타입스크립트에는 숫자형 리터럴타입도 있다.
function rollDice(): 1 | 2 | 3 | 4 | 5 | 6 {
    return (Math.floor(Math.random() * 6) + 1) as 1 | 2 | 3 | 4 | 5 | 6;
}

const result = rollDice();
```

```typescript
//숫자형 리터럴은 설정값을 설명할때 주로 사용됩니다.
declare function setMap(config: MapConfig): void;
//declare는 컴파일러에게 특정한 변수가 있다고 선언하는 키워드이다.
//전역변수나 .d.ts파일 만들때 사용
//자바스크립트로 이미 작성된 함수가 있는 경우에 타입만 정의하여 컴파일러에게 알리기 위함

interface MapConfig{
    lng: number;
    lat: number;
    tileSize: 8 | 16 | 32;
}

setupMap({lng: -73.935242, lat: 40.73061, tileSize: 16});
```