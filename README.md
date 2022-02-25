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
{ /* Type Checking */ "strict": false, }
```

### in JavaScript

- null  
나중에 값을 지정하기 위해 비워둔 상태, runtime에서 `typeof` 연산자를 사용하면, `object`를 반환한다.

- undefined  
값이 지정되지 않은 상태, runtime에서 `typeof` 연산자를 사용하면, `undefined`를 반환한다.

# Non-Primitive Type

## Object
여러 타입의 요소들을 `key: "value"` 형태로 묶어놓은 자료형  
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

# Any
자료형을 제한하지 않을 때, 나중에 정할 때 사용한다.  
(type safety를 위해 최대한 사용하지 않는 것이 권장된다.)
```ts
function returnAny(message: any): any {
  console.log(message);
}
returnAny('gran_dev_hun');
returnAny(23);
```