# 타입스크립트 알아보기
- 모든 자바스크립트 프로그램이 타입스크립트지만, 반대는 아니다.
~~~typescript
function greet(who: string) {
    console.log('Hello', who);
}
~~~
- 위와같은 타입스크립트에서 사용하는 :string은 타입스크립트에서만 사용.
- 타입스크립트 컴파일러는 일반 자바스크립트 프로그램에도 유용하다.
- 타입스크립트의 목표 중 하나는 런타임에 오류를 발생시킬 코드를 미리 찾아내ㅔ는 것이다.
  - 타입스크립트가 정적타입 시스템이라는 것은 바로 이런 특징 때문
- 오류를 발생하지는 않지만 의도와 다르게 동작하는 코드도 있다.
~~~javascript
const states = [
    {name: 'Alabama', capital: 'Seoul'},
    {name: 'kil hyun', capital: 'Busan'},
    {name: 'yong jun', capital: 'Asan'}
];

for(cosnt state of states) {
    console.log(state.capitol); // capital이 아닌 capitol
}

결과
undefined
undefined
undefiend
~~~
- 타입스크립트는 이런 해결책도 제시한다. 
- 또한 타입스크립트는 런타임 동작을 모델링한다.
- ex: const x = 2 + '3'; // 정상 string 타입입니다.
- 반대로 정상 동작하는 코드에 오류를 표기하기도 합니다. 런타임 오류는 발생하지 않지만 타입체커는 문제점을 표시합니다.
~~~typescript
const a = null + 7; // '+' 연산자를 ... 형식에 적용할 수 없습니다 에러 표출
const b = [] + 12; // '+' 연산자를 ... 형식에 적용할 수 없습니다 에러 표출
alert('Hello', 'TypeScript') // 0-1개의 인수가 필요한데 2개를 가져왔습니다 경고 표시
~~~
- 프로그램에 오류가 발생하지 않더라도 타입체커가 오류를 표시한다.
- 해당 안에 변수가 있는지 확인하려면 if('변수명' in 객체변수명) 
~~~typescript
function calculateArea(shape: Shape) {
    if('height' in shape) {
        return shape.width * shape.height;
    }
}
~~~
- 그러나 결국은 객체로 선언하는게 더 나은것 같다. if(shape instanceof Rectangle) 와 같이..
- 구조적 타이핑(structural typing)이란 호환될 경우 그냥 선언하지 않아도 사용 가능한것이다.(딱히 그 객체가 아니더라도, 그 객체에 해당하는 변수가 선언되어 있으무ㅕㄴ 그 결곽값을 출력해준다.)
- 좋든 실든 열려있다. (젠장..)

~~~typescript
interface NamedVector {
    name:string;
    g
}

function calculateLength(v: Vector2D) {
    return Math.sqrt(v.x * v.y + v.y * v.y);
}

const v: NamedVector = {x:3, y:4, name:'Zee'};
calculateLenth(v); // 정상으로 나온다.
~~~
- 키 값을 뽑고 싶으면 for(const axis of Objects.keys(v)) {v[axis]  } 로 가능..?
- 그렇기 때문에 확장자 타입은 any로 선언되는 것이고, 다른 속성도 들어올 수 있기 때문에 해야한다
- ex) const vec3D = {x: 3, y: 4, z: 1, address: '123 broadway'}; 일 경우 문제가 발생한다. 
- 구조적 타이핑은 클래스와 관련된 할당문에서도 당황스러운 결과를 보여준다.

~~~typescript
class C {
    foo: string;

    constructor(foo: string) {
        this.foo = foo;
    }
}

const c = new C('instance of C');
const d:C = {foo: 'object leteral'}; //정상
~~~
- 그러니깐 @SerializedOption때문에 결과값이 잘 나온게 아니라, 결론적으로 이건 구조적 타이핑때문에 저렇게 변수선언이 가능해서 안에 클래스로 할당된것으로 보여진다.
- 젠장 재밌네.,.
- any는 절대로 쓰지 말라

# 타입스크립트의 타입 시스템
- 타입시스템이란 무엇인지, 어떻게 사용할지, 무엇을 결정해야 할 지, 가급적 사용하지 말아야 할 기능은 무엇인지 배움

## 아이템6 편집기를 사용하여 타입 시스템 탐색하기
- 자바스크립트에서 typeof null 은 object 이다.
- never타입이 있다. 이것은 아무 값도 포함하지 않는 공집합이다. 가장 작은 집합. 아무런 값도 할당할 수 없다.
- 유닛타입 or 리터럴 타입 한가지 값만 포함하는 타입 type A= 'A';
- 유니온타입은 두개 혹은 세개로 묶는 것. type AB = 'A'| 'B';
- const a: AB = 'A';
~~~typescript
const people = ['alice', 'bob'].map(name => {
  const person:Person = {name}
  return person
});

//이걸 사용해라~~
const people = ['alice', 'bob'].map(
        (name): Person => ({name})  
);

//근데 이건 반환타입이 없으므로 에러를 반환한다. 그러므로 이렇게 사용해야 완성된다
const people: Person[] = ['alice', 'bob'].map(
        (name): Person => ({name})
);
~~~
- 접미사로 쓰이는 !는 그 값이 null이 아니라는 단언문으로 해석된다.

- 함수 타입의 선언은 불필요한 코드의 반복을 줄여준다. 
~~~typescript
function add(a: number, b: number) { return a + b;}
function sub(a: number, b: number) { return a - b;}
function mul(a: number, b: number) { return a * b;}
function div(a: number, b: number) { return a / b;}

//아래와 같이 변경 가능핮다
type binaryFn(a: number, b:number) => number;

const add: binaryFn = (a,b) => a + b;
const sub: binaryFn = (a,b) => a - b;
const mul: binaryFn = (a,b) => a * b;
const div: binaryFn = (a,b) => a / b;
~~~
- typescript는 typeof를 잘 선언해 주는게 중요한것 같아.

## 아이템 13 타입과 인터페이스 차이점 알기
- 타입스크립트에서 명명된 타입을 정의하는 방법
  - 타입을 사용
    - type TState = {name: string; capital: string;}
  - 인터페이스 사용
    - interface IState {name: string; capital: string;}
- 같은 상황에서는 동일한 방법으로 명명된 타입을 정의해 일관성을 유지해야 한다. 
- 그러려면 하나의 타입에 대해 두가지 방법 모두 사용해서 정의할줄 알아야 한다.
- 둘다 추가속성과 함께 할당한다면 오류가 발생
~~~typescript
const wyming: TState = {
    name:'wyming',
    capital: 'Choi',
    populaction: 500_000 // ... 형식은 할당 불가.
}
~~~
### 공통점
- 인덱스 시그니처의 경우 인터페이스와 타입 모두 사용 가능
~~~typescript
type TDict = {[key: string]: string};
interface IDict {
    [key: string]: string;
}
~~~
- 또한 함수 타입도 인터페이스나 타입으로 정의 가능

~~~typescript
type TFn = (x: number) => string;
interface IFn {
  (x:number): string;
}

const toStrT: TFn = x => ''+ x; //정상
const toStrI: IFn = x => ''+ x; //정상
~~~
- 사실 이런 단순한 함수 타입에는 타입 별칭(alias)이 더 낫다.
- 인터페이스는 타입을, 타입은 인터페이스를 확장 가능하다.
- 그러나 인터페이스는 유니온타입같은 복잡한 타입을 확장하지는 못한다.
- 복잡한 타입을 확장하고 싶으면 타입과 &을 사용
- 클래스 구현도 타입과 인터페이스 둘다 사용 가능

### 차이점
- 인터페이스는 타입을 확장할 수 있지만 유니온은 불가능
- 인터페이스는 보강이 가능...?? => 선언병합
~~~typescript
//선언병합이다 이말이다
interface IState {
    name: string;
    capital: string;
}

interface ISate {
    population: number;
}

const wyoming: ISate = {
    name: 'Wyoming',
    capital:'Cheyenne',
    population: 500_000
}; // 정상... 왜..??
//타입스크립트에서는 똑같은 인터페이스를 선언할 경우 이것을 합쳐버린다. ㅋㅋㅋㅋㅋㅋㅋㅋ
~~~
### 결론
- 복잡한 타입이라면 고민할 필요 없이 타입별칭 사용
- 타입과 인터페이스 두 가지 방법으로 모두 표현할 수 있는 간단한 객체 타입이라면 일관성과 보강의 관점에서 고려해봐야 한다.
- 일관되게 뭘 하나 사용했다면 그 방식으로 사용해라.
- 어떤 API에 대한 타입 선언을 작성해야 한다면 인터페이스가 좋다.(병합 가능하므로)
- 그러나 내부적으로 사용되는 타입에 대해서는 선언 병합이 발생하면 잘못된것이므로 타입을 사용해야 한다.

## 아이템 14 타입 연산과 제너릭 사용으로 반복 줄이기

~~~typescript
console.log('Cylinder 1 x 1 ', 'Surface area:', 6.283185 * 1 * 1 + 6.283185 * 1 * 1, 'Volume: ', 3.14159 * 1 * 1 * 1);
console.log('Cylinder 1 x 2 ', 'Surface area:', 6.283185 * 1 * 1 + 6.283185 * 2 * 1, 'Volume: ', 3.14159 * 1 * 2 * 1);
console.log('Cylinder 2 x 1 ', 'Surface area:', 6.283185 * 2 * 1 + 6.283185 * 2 * 1, 'Volume: ', 3.14159 * 2 * 2 * 1);

//아래와 같이 변환 가능하다.
const surfaceArea = (r, h) => 2 * Math.PI * r * (r + h);
const volume = (r, h) => Math.PI * r * r * h;
for (const [r, h] of [[1, 1], [1, 2], [2, 1]]) {
  console.log(`Cylender ${r} x ${h}`, `Surface area: ${surfaceArea(r, h)}`, `Volume: ${volume(r, h)}`);
}
~~~
- DRY(don't repeat yourself)
- 그러나 타입을 간과하면 안된다

~~~typescript
interface Person {
    firstName: string;
    lastName: string;
}

interface PersonWithBrithDate {
    firstName: string;
    lastName: string;
    birth: Date;
}
~~~
- 타입 중복은 코드 중복만큼 많은 문제를 발생시킵니다.
- 위의 경우 birth를 추가했지만 다른 타입이 되어버린다.
- 타입간에 매핑하는 방법을 익히면, 타입 정의에서도 DRY의 장점 적용가능
- 반복을 줄이는 가장 간단한 방법은 타입에 이름을 붙이는 것이다.
- 아래의 거리 계산 함수에는 타입이 반복적으로 등장한다.

~~~typescript
function distance(a: {x: number, y: number}, b:{x: number, y: number}) {
    return Math.sqrt(Math.pow(a.x - b.x, 2)+ Math.pow(a.y -b.y, 2));
}
~~~
- 코드를 수정해 타입에 이름을 붙여보자!!
~~~typescript
interface Point2D {
    x: number;
    y: number;

}

function distance(a: Point2D, b: Point2D) {
  return Math.sqrt(Math.pow(a.x - b.x, 2)+ Math.pow(a.y -b.y, 2));
}
~~~
- 중복된 타입은 종종 문법에 의해서 가려지기도 한다. 
- 예를 들어 몇몇 함수가 같은 타입 시그니쳐를 공유하고 있따고 해보다.

~~~typescript
function get(url: string, opts: Options): Promise<Response> { /* ... */}
function post(url: string, opts: Options): Promise<Response> { /* ... */}

type HTTPFuntion = (url: string, opts: Options) => Promise<Response>;
const get: HTTPFuntion = (url, opts) => {/* ... */};
const post: HTTPFuntion = (url, opts) => {/* ... */};
~~~
- 인터페이스에도 중복된 내용이 들어간다면?? 변환하자!!
~~~typescript
interface State {
    userId: string;
    pageTitle: string;
    recentFiles: string[];
    pageContents: string;
}

interface  TopNavState {
    userId: string;
    pageTitle: string;
    recentFiles: string[];
}

//타입을 합쳐버리자!!!
type TopNavState = {
    userId: State['userId'];
    pageTitle: State['pageTitle'];
    recentFiles: State['recentFiles'];
}

//이건 매핑된 타입을 통해 더 간단하게 변경 가능하다.
type TopNavState = {
    [k in 'userId'| 'pageTitle'| 'recentFiles']:State[k]
}

a
~~~
