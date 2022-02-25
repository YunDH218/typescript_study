[목차](./README.md "목차")
# Type System

Type System에는 두 가지가 있다.

- 컴파일러에게 사용하는 타입을 명시적으로 지정하는 시스템
- 컴파일러가 자동으로 타입을 추론하는 시스템  

TypeScript에서는 타입을 명시적으로 지정할 수 있고 지정하지 않는다면 컴파일러가 자동으로 타입을 추론한다.

<br><br>

# 작성자와 사용자의 관점으로 코드 바라보기
함수는 형태가 정해져있다. 이 함수를 구현한 구현자가 있을 것이고 구현된 함수를 사용하는 사용자도 있을 것이다. 각각의 관점에서 코드를 보도록 하자.  

Javascript는 타입을 자동으로 추론한다. 이 때문에 함수 사용법에 대한 오해를 야기하기도 한다. 사용자가 함수의 사용법을 숙지하지 않았다면 작성자의 의도에 맞지 않게 사용할 수 있다.
```js
// descriper expected: n ← number
function double(n) {
  return n * 2
}

// user
console.log(double('Hello!')) // NaN
```

## noImplicitAny

타입스크립트를 사용하더라도 추론에 의지한다면 같은 결과가 나타난다. 하지만 noImplicitAny 옵션을 켜면 추론 중 `any`라고 판단하게 되면 컴파일 에러를 발생시켜 명시적으로 지정하도록 유도한다.

- `tsconfig.json`
```json
/* Type Checking */ "noImplicitAny": true
```
- `main.ts`
```ts
function double(n) {  // error TS706: Parameter 'a' implicitly has an 'any' type.
  return n * 2
}
```

## strictNullChecks

명시적으로 return type을 지정하지 않은 경우에는 `number` 로 추론된다.
```ts
function doubleWhenPositive(n: number) {
  if (n > 0) {
    return a * 2;
  }
}

console.log(doubleWhenPositive(5));
console.log(doubleWhenPositive(-5) + 5);  // error TS2532: Object is possibly 'undefined'
```

이때 strictNullChecks 옵션을 켜면 모든 타입에 자동으로 포함되어 있는 null과 undefined를 제거해준다. 하지만 이 경우에도 문제가 발생한다. 명시적으로 return type을 지정하지 않은 경우에는 모든 경우를 `number | undefined` 로 추론하기 때문이다.
따라서 return type도 명시적으로 지정하는 것이 안전하다. 

## noImplicitReturns

noImplicitReturns 옵션을 켜면 If문에 해당하지 않는 경우에도 `return`을 명시할 수 있도록 강제한다.

- `tsconfig.json`
```json
/* Type Checking */ "noImplicitAny": true
```
- `main.ts`
```ts
function doubleWhenPositive(n: number): number {
  if (n > 0) {
    return a * 2;
  }
  return;
}
```

## 나만의 타입 만들기

매개변수에 Object가 들어가는 경우에는 type을 명시하지 않으면 사용자가 함수를 숙지하지 못했을 때 type을 잘못 넣어도 error가 나는 것이 아니라 `undefined`나 `NaN`같은 값이 그대로 출력될 수 있다. 따라서 type을 명시해주어야 한다.

```ts
function nameCard(obj: { name: string; age: number }): string {
  return `my name is ${name}, and I'm ${age} years old.`;
}
```

하지만 매번 타입을 명시하기에는 번거롭다. 이를 해소하기 위해 `interface` 또는 `type` 키워드를 사용해 타입을 정의할 수 있다.

```ts
interface PersonInterface { name: string; age: number };
type PersonTypeAlias = { name: string; age: number };

function nameCard(obj: PersonInterface): string {
  return `my name is ${name}, and I'm ${age} years old.`;
}
```

<br><br>

# Structual Type System vs Nominal Type System

## Structual Type System - 구조가 같으면 같은 타입이다

TypeScript는 Structual Type System을 따른다.

```ts
interface PersonInterface { name: string; age: number };
type PersonTypeAlias = { name: string; age: number };

let personInterface: PersonInterface = {} as any;
let personTypeAlias: PersonTypeAlias = {} as any;

personInterface = personTypeAlias;
personTypeAlias = personInterface;
```

## Nominal Type System - 구조가 같아도 이름이 다르면, 다른 타입이다.

C, Java 등의 언어는 Nominal Type System을 따른다.  
TypeScript에서도 의도적으로 구조가 같지만 다른 데이터 타입을 만들기 위해서는 아래와 같은 방법을 쓸 수 있다.

```ts
type PersonID = string & { readonly brand: unique symbol };

function PersonID(id: string): PersonID {
  return id as PersonID;
}
function getPersonByID(id: personID) {}

getPersonByID(PersonID('id-abcde'));
getPersonByID('id-abcde');  // error 발생 : 같은 string 구조이지만 다른 타입으로 정의했기 때문에 할당할 수 없다.
```

<br><br>

# Type Compatibility(타입 호환성)

- supertype에는 subtype을 할당할 수 있다.
- subtype에는 supertype을 할당할 수 없다.
- 같거나 subtype인 경우, 할당이 가능하다 : 공변
- 함수의 매개변수 타입만 같거나 supertype인 경우, 할당이 가능하다 : 반병

다음 각각의 상황에서, sub#는 sup#의 subtype이다.
```ts
let sub1: 1 = 1;
let sup1: number = sub1;
sub1 = sup1;  // error: Type 'number' is not assignable to type '1'.

let sub2: number[] = [1];
let sup2: object = sub2;
sub2 = sup2;  // error: Type is missing the following properties from type 'number[]': length, pop, push, concat, and 16 more.

let sub3: [number, number] = [1, 2];
let sup3: number[] = sub3;
sub3 = sup3;  // error: Type 'number[]' is not assignable to type '[number, number]'. Target requires 2 element(s) but source may have fewer.

let sub4: number = 1;
let sup4: any = sub4;
sub4 = sup4;  // It's OK

let sub5: never = 0 as never;
let sup5: number = sub5;
sub5 = sup5;  // error: Type 'number' is not assignable to type 'never'.

class Animal {}
class Dog extends Animal {
  bark() {}

let sub6: Dog = new Dog();
let sup6: Animal = sub6;
sub6 = sup6; // error: Property 'bark' is missing in type 'SubAnimal' but required in type 'SubDog'
}

/* 공변 */
let sub7: string = '';
let sup7: string | number = sub7

let sub8: { a: string; b: number } = {a: '', b: 1};
let sup8: { a: string | number; b: number } = sub8;

let sub9: Array<{ a: string; b: number }> = [{ a: '', b: 1}];
let sup9: Array<{ a: string | number; b: number }> = sub8;


/* 반병 */
class Person {}
class Developer extends Person {
  coding() {}
}
class StartupDeveloper extends Developer {
  burning() {}
}

function tellme(f: (d: Developer) => Developer) {}

// Developer => Developer에 Developer => Developer를 할당하는 경우
tellme(function dToD(d: Developer): Developer {
  return new Developer();
});

// Developer => Developer에 Person => Developer를 할당하는 경우
tellme(function pToD(p: Person): Developer {
  return new Developer();
});

// Developer => Developer에 StartupDeveloper => Developer를 할당하는 경우
tellme(function sToD(s: StartupDeveloper): Developer {
  return new Developer();
}); // 논리적으로 문제가 되는 경우이지만 strictFunction 옵션을 켜지 않으면 사용 가능(켜면 error를 통해 경고)
```

<br><br>

# Type Alias

Primitive, Union Type, Tuple, Function 등 기타 작성해야하는 타입을 다른 이름으로 지정할 수 있다. 만들어진 타입을 참조하여 사용하는 것이지 새로운 타입을 만드는 개념은 아니다.

## Aliasing Primitive

큰 의미를 가지지 않는다.

```ts
type MyStringType = string;
```

## Aliasing Union Type

Union Type에서 길게 명시해야하는 것을 더 간단하게 명시하기 위해 사용한다.

```ts
type StringOrNumber: string | number;
let son: StringOrNumber = 0;
son = 'zero';
```

## Aliasing Tuple

Tuple에서 길게 명시해야하는 것을 더 간단하게 명시하기 위해 사용한다.

```ts
type StringAndNumber: [string, number];
```

## Aliasing Function

```ts
type EatType: (food: string) => void;
```

## Interface와의 구분

기술적으로 차이가 있기는 하지만 개발자마다 다른 기준으로 구분한다. 강사는 말하길 목적이나 존재가치가 분명하면 `interface`, 그렇지 않고 별칭으로서 사용한다면 `type`을 사용한다고 한다.