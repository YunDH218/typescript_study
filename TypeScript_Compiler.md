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

### typeRoots

### types

### target

### lib

### outDir

### outFile

### rootDir

### strict