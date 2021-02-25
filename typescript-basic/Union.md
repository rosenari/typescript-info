#### 유니언과 교차타입

> 유니언 타입
> number 또는 string을 매개변수로 기대하는 함수를 작성하고 싶을때
> 유니업타입(여러타입중 하나가 될수있는 타입)을 통해 작성할 수 있다.

```typescript
function padLeft(value: string,padding: string | number) {
    //...
}

let indentedString = padLeft("Hello World" ,true);
```

> 공통 필드를 갖는 유니언

```typescript
interface Bird{
    fly(): void;
    layEgg(): void;
}

interface Fish {
    swim(): void;
    layEggs(): void;
}

declare function getSmallPet(): Fish | Bird;

let pet = getSmallPet();

pet.layEgg(); //유니언에 있는 모든 타입에 있는 메서드이다.

pet.swim(); //실제로 런타임에 Fish일경우 호출하는 것은 오류
```

> 유니언 구별하기
> 공통 필드를 가진 세 가지타입의 유니언이 있을때, 이를 구분하기위해 switch문을 활용할수 있다.

```typescript
type NetworkLoadingState = {
    state: "loading";
};

type NetworkFailedState = {
    state: "failed";
    code: number;
};

type NetworkSuccessState = {
    state: "success";
    response: {
        title: string;
        duration: number;
        summary: string;
    };
};

type NetworkState =
    | NetworkLoadingState
    | NetworkFailedState
    | NetworkSuccessState;

function networkStatus(state: NetworkState): string{
    
    state.code;
    //모든 타입에 공유되지 않는 프로퍼티를 접근하려는 시도는 오류를 발생시킨다.

    //switch문을 사용해 유니언 타입을 좁혀나갈수 있다.
    switch(state.state){
        case "loading":
            return "Downloading...";
        case "failed":
            return `Error ${state.code} downding..`;
        case "success":
            return `Downloaded ${state.response.title} - ${state.response.summary}`;
    }
}
```

> 교차타입
> 교차타입은 여러타입을 하나로 결합합니다.
> 기존 타입을 합쳐 필요한 기능을 모두가진 단일 타입을 얻을 수 있습니다.

```typescript
interface ErrorHandling{
    success: boolean;
    error?: { message: string };
}

interface ArtworksData {
    artworks: { title: string }[];
}

interface ArtistsData{
    artists: { name: string }[];
}

type ArtworksResponse = ArtworksData & ErrorHandling;//교차타입
type ArtistsResponse = ArtistsData & ErrorHandling;//교차타입

const handleArtistsResponse = (response: ArtistsResponse) => {
    if(response.error){
        console.error(response.error.message);
        return;
    }

    console.log(response.artists);
}
```

> 교차타입을 활용한 믹스인 패턴
```typescript
class Person{
    constructor(public name: string) {};
}

interface Loggable{
    log(name: string): void;
}

class ConsoleLogger implements Loggable {
    log(name: string){
        console.log(`Hello, I'm ${name}.`);
    }
}

//두개의 객체를 받아 하나로 합치는 함수이다.
function extends<First extends {},Second extends {}>(
    first: First,
    second: Second
): First & Second {
    const result: Partial<First & Second> = {};
    //Partial타입은 제네릭 타입의 선언타입의 형태를 유지하되,
    //모든 프로퍼티에 대하여 타입을 선택적타입으로 변경한다.
    
    for(const prop in first){
        if(first.hasOwnProperty(prop)){
            (result as First)[prop] = first[prop];
        }
    }
    //first 객체의 모든 프로퍼티와 값을 result에 부여

    for(const prop in second){
        if(second.hasOwnProperty(prop)){
            (result as Second)[prop] = second[prop];
        }
    }

    //second 객체의 모든 프로퍼티와 값을 result에 부여

    return result as First & Second; //교차타입을 사용해 result 리턴
}
```