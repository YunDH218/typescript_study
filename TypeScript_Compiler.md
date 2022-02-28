[목차](./README.md "목차")
# Compilation Context
참고 : [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/project/compilation-context "compilation-context")  
어떤 방식으로 compile할 것인지에 대해 규명한 맥락. tsconfig.json 파일을 통해 정의할 수 있다.

<br><br>

# tsconfig schema
참고 : [JSON Schema Store](https://json.schemastore.org/tsconfig "tsconfig.json")  
최상위 Property들만 살펴보기로 한다.

## compileOnSave
파일을 저장할 때마다 compile할 지 여부를 설정한다.  
type: boolean(default: false)

## extends
상속 받아올 configuration file의 경로를 저장하는 property. tsconfig는 다른 파일을 상속받아 그 안에 추가해서 설정하는 방법을 지원한다.  
참고: [tsconfig/bases](https://github.com/tsconfig/bases "github.com")에서 유용한 설정들을 통해 완성도있는 결과물을 낼 수 있다.  
type: string

## files, include, exclude
어떤 directory 안에 있는 어떤 파일을 compile할 것인지 설정하는 속성들 

### files
string의 배열 안에 compile할 파일의 경로를 명시한다. files 또는 include 속성을 지정하지 않는다면 compiler는 기본적으로 모든 파일을 compile한다.  
type: string[]

### exclude
string의 배열 안에 compile에 제외할 파일을 명시한다. include 속성에 명시된 파일은 제외시킬 수 있지만, files속성에 명시된 파일은 제외시킬 수 없다. Glob pattern(.gitignore에서 쓰는 방식과 동일)으로 작성한다.  
type: string[]
default: ["node_modules", "bower_components", "jspm_packages", "<outDir>"]

### include
string의 배열 안에 compile에 포함할 파일을 명시한다. Glob pattern으로 작성한다.  
type: string[]

## compileOptions

### typeRoots, types

우리가 library를 사용할 때 library는 JavaScript로 작성된 파일이기 때문에 TyprScript에서 바로 사용할 수 없다. TypeScript에서 사용하기 위해서는 type을 지정해주는 도구(type definition system)가 필요하다. TypeScript 2.0부터는 자체적으로 지원하는데 이를 사용하는 방법은 다음과 같다.  
- 예시 - react를 설치할 때

```
npm install --save-dev @types/react
```

typeRoots에 경로를 지정하면 경로들 안에서 사용하고, 아무 설정도 하지 않으면 node_module/@types의 모든 경로를 찾아서 사용한다.  
types에 모듈의 이름을 지정하면 그 모듈들을 찾아 사용한다. (types와 typeRoots 둘 중 하나만 사용)  
type: string[]

### target

build의 결과물을 어떤 버전으로 할 것인지 정해서 최종적으로 어떤 실행환경에서 사용할 것인지 지정한다. 지정할 수 있는 런타임은 다음과 같다.  
- ES3, ES5, ES6, ES2015, ES2016, ES2017, ES2018, ES2019, ES2020, ESNEXT, ...  

예를 들어 ES5 이하 버전에서는 Arrow Function이 지원되지 않기 때문에 `"target": "ES5"`로 지정한다면 Arrow Function을 function 키워드를 사용한 함수 선언 형태로 바꾸어준다.  
default: "ES3"  
type: string

### lib
기본 type definition library로 어떤 것을 사용할 것인지 정한다. 
type: string[]

### outDir, outFile, rootDir

- outFile  
모든 output을 하나의 JavaScript 파일로 묶어준다. 단, 모듈이 시스템이나 amd와 같은 형태를 지원해야 한다.  
type: string

- outDir  
compile하여 파일을 하나의 out directory 안에 저장한다.  
type: string (out directory의 이름)

- rootDir  
compile할 root directory를 설정한다.  
type: string (root directory의 이름)

### strict

안전성을 위해 모든 strict type checking option을 true로 설정한다.
- --noImplicitAny  
명시적이지 않게 any 타입을 사용하여, 표현식과 선언에 사용하면, 에러를 발생시킨다.
- --noImplicitThis  
명시적이지 않게 any 타입을 사용하여, this 표현식에 사용하면, 에러를 발생시킨다.
- --strictNullChecks  
모든 타입은 null, undefined 값을 가질 수 없고, 가지려면 union type을 이용해서 직접 명시해야 한다. (any는 null과 undifined를 가질 수 있고, void는 undifined를 가질 수 있다.)
- --strictFunctionTypes  
함수 타입에 대한 bivariant 매개변수 검사를 비활성화한다.

- --strictPropertyInitialization  
정의되지 않은 클래스의 속성이 생성자에서 초기화되었는지 확인한다. (--strictNullChecks 옵션이 true일 때 사용 가능)

- --strictBindCallApply  
`bind`, `call`, `apply`에 대해 더 엄격하게 검사한다.

- --alwaysStrict  
각 소스파일에 대해 JavaScript의 strict mode로 코드를 분석한다. "use strict"를 해제한다.