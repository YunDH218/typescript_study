[목차](./README.md "목차")
# Primitive Type (원시 데이터)

reference 형태가 아닌 실제 값을 저장하는 자료형
- boolean
- number
- string
- symbol
- null
- undefined

literal(symbol 제외), wrpper 객체로 만들 수 있다.  
(wrapper는 권장하지 않는다. object)

## Boolean

true또는 false의 상태를 할당하는 자료형
```ts
const isTrue: boolean = true;
console.log(typeof isDone); // 'boolean'
```

## Number

숫자를 나타내는 타입(floating number)
```ts
const decimal: number = 6; // 10진수
const hex: number = 0xffff; // 16진수
const binary: number = 0b1010; // 2진수
const octal: number = 0o747; // 8진수
const notANumber: number = NaN; // 숫자가 아닌 데이터
const underscoreNumber: number = 1_000_000; // underscore(_)로 천 단위 구분 가능
```

## String

텍스트 형식을 나타내기 위한 자료형. backtick기호(`) 또는 큰 따옴표(")나 작은 따옴표(')를 사용한다.

```ts
const myName: string = `gran_dev_hun`;
```

### Template String

표현식을 넣을 수 있는 문자열 ``` `${ expr }` ```와 같은형태로 사용
```ts
// template string을 사용하지 않는 경우
const myName: string = `gran_dev_hun`;
let introduce: string = `Hello, my name is ` + myName + `.`;
// template string 사용
introduce = `Hello, my name is ${ myName }.`
```

## Symbol

`new Symbol`로 사용할 수 없음, `Symbol()`함수를 사용해서 symbol 타입을 생성할 수 있다.  
symbol을 사용할 수 있는 환경을 만들기 위해 tsconfig.json 파일의 "lib"를 아래와 같이 조정해주어야 한다.
```json
{ /* Language and Environment */ "lib": ["ES2015", "DOM"], }
```
원시 데이터 타입의 값을 담아서 사용한다.  
symbol은 고유하고 수정 불가능하기 때문에 접근을 제어하는 데 쓰는 경우가 많았다.

```ts
// 같은 symbol을 비교해보면 false 값이 나온다.
console.log(Symbol('xyz')===Symbol('xyz')); // false

// 접근을 제어할 때 사용
const sym = Symbol();

const obj = {
  [sym]: "value",
};
console.log(obj['sym']); // undefined
console.log(obj[Symbol()]); // undefined
console.log(obj[sym]); // value
```

## Undefined & Null

다른 자료형들의 subtype으로서(tsconfig.json 파일을 따로 설정하지 않는다면), 정의되지 않았거나(`undefined`) 값을 갖지 않는다는 상태(`null`)를 나타내기 위해 사용한다.  
```json
{ /* Type Checking */ "strict": true, }
```

### in JavaScript

- null  
나중에 값을 지정하기 위해 비워둔 상태, runtime에서 `typeof` 연산자를 사용하면, `object`를 반환한다.

- undefined  
값이 지정되지 않은 상태, runtime에서 `typeof` 연산자를 사용하면, `undefined`를 반환한다.

<br><br>

# Non-Primitive Type

## Object
여러 타입의 요소들을 `key: value` 형태로 묶어놓은 자료형  
literal, wrpper 객체로 만들 수 있다.
```ts
const person1 = { name: 'gran_dev_hun', age: 23 }; // literal
const person2 = Object.create({ name: 'gran_dev_hun', age: 23 }); // wrraper
// const person2 = Object.create(23) // error: primitive types can't be use as parameter of Object.create().
```

## Array
같은 타입의 요소(item)들을 순서대로 묶어놓은 자료형
```ts
let list1 = [1, 2, 3]; // 명시하지 않아도 자동으로 number로 지정됨
let list2: number[] = [1, 2, 3]; // 자료형을 명시하는 방법 1 (권장)
let list3: Array<number> = [1, 2, 3]; // 자료형을 명시하는 방법 2
let list2: (number | string)[] = [1, 2, 3, "4"]; // union 타입으로도 쓸 수 있음
```

## Tuple
각 인덱스에 있는 요소의 자료형을 명시할 때 사용한다.
```ts
let person: [string, number]; // 첫 번째 요소는 string, 두 번째 요소는 number
person = ["gran_dev_hun", 23];
// person = [23, "gran_dev_hun"]; // error: type is not matched.
const [name, age] = person; // const name: string, const age: number
```

## Any
자료형을 제한하지 않을 때, 값을 동적으로 할당할 때 사용한다. (type safety를 위해 최대한 사용하지 않는 것이 권장된다.)
```ts
function returnAny(message: any): any {
  console.log(message);
}
returnAny('gran_dev_hun');
returnAny(23);
```
any는 개체를 통해 전파된다. any의 property의 자료형은 any가 된다.
```ts
const looselyTyped: any = {};
const d = looselyTyped.a.b.c; // 지정하지 않은 property나 method를 입력해도 error가 발생하지 않는다. --> type safety를 잃는다. const d: any
```
any가 필요한 상황에서 any의 leaking을 막기 위해서는 속성마다 자료형을 명시하는 작업이 필요하다.
```ts
function addOne(obj: any) {
  const a: number = obj.num; // 속성의 자료형 명시(명시하지 않았다면 a는 any)
  return a + 1;
}
const b = addOne({ num: 0 });
b.indexOf("0"); // error: 자료형이 any가 아닌 number이므로 string의 method 사용 불가
```

## Unknown
값을 동적으로 할당할 때 사용한다. any와 다르게 전파되지 않기 때문에 더 안전하다.
```ts
declare const maybe: unknown;
if(maybe === true) {
  const aBoolean: boolean = maybe; // 해당 if문 안에서 maybe는 boolean data가 된다.
  const aString: string = maybe; // type error 발생, any보다 안전하다.
}
if(typeof maybe === "string") {
  const aString: string = maybe; // 해당 if문 안에서 maybe는 string data가 된다.
  const aBoolean: boolean = maybe; // type error 발생, any보다 안전하다.
}
```
위와 같은 작업을 통해 자료형을 제한해주는 것을 type-guard라고 한다.

## Never
어떠한 type도 아니라는 의미. 일반적으로 `return`에 사용된다.
```ts
function error(message: string): never {  // 어떠한 값도 반환되지 않음
  throw new Error(message);
}
function infiniteLoop(): never {
  while (true) {}
}
```
모든 타입의 subtype이며, 모든 타입에 할당할 수 있다. 하지만 never에는 어떤 것도 할당할 수 없다.(any 포함) 잘못된 타입을 넣는 실수를 막고자 할 때 사용하기도 한다.
```ts
type Indexable<S> = S extends string ? S & { [index: string]: any } : never;
type ObjeectIdexable = Indexable<{}>; // string이 아닌 경우의 실수를 막도록 never가 된다.
```

## Void
빈 타입. 값을 할당할 수 없다. JS는 다른 언어와 달리 undefined가 있기 때문에 사실상 void가 필요하지 않다. return type으로 사용.
```ts
function returnVoid() {
  return;
}
const r = returnVoid(); // const r: void
```