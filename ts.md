https://24kcs.github.io/vue3_study/

# 1. 初识ts

## 1. ts 安装

```i
npm install -g typescript
tsc -V
```



## 2. vscode 自动编译

```json
// 生成配置文件tsconfig.json
tsc --init

// 修改 tsconfig.json 配置
"outDir":".js",   // 编译文件输出目录
"strict":false    // 严格模式

// 启动监视任务
终端 => 运行任务 => 监视tsconfig.json
```



## 3. 类型注解

给 person 函数的参数添加 : string 类型注解，如下

```ts
function greeter (person: string) {
  return 'Hello, ' + person
}

let user = 'Yee'

console.log(greeter(user))
```



## 4. 接口

使用接口来描述一个拥有 firstName 和 lastName 字段的对象。 在 TypeScript 里，只在两个类型内部的结构兼容，那么这两个类型就是兼容的。 这就允许我们在实现接口时候只要保证包含了接口要求的结构就可以，而不必明确地使用 implements 语句。

```ts
interface Person {
  firstName: string
  lastName: string
}

function greeter (person: Person) {
  return 'Hello, ' + person.firstName + ' ' + person.lastName
}

let user = {
  firstName: 'Yee',
  lastName: 'Huang'
}

console.log(greeter(user))
```



## 5. 类

创建一个 User 类，它带有一个构造函数和一些公共字段。因为类的字段包含了接口所需要的字段，所以他们能很好的兼容。

```ts
class User {
  fullName: string
  firstName: string
  lastName: string

  constructor (firstName: string, lastName: string) {
    this.firstName = firstName
    this.lastName = lastName
    this.fullName = firstName + ' ' + lastName
  }
}

interface Person {
  firstName: string
  lastName: string
}

function greeter (person: Person) {
  return 'Hello, ' + person.firstName + ' ' + person.lastName
}

let user = new User('Yee', 'Huang')

console.log(greeter(user))
```

# 2. ts 常用语法

## 1. 基础类型

### 1. 布尔值

```ts
let isDone: boolean = false;
isDone = true;
// isDone = 2 // error
```

### 2. 数字

```ts
let a1: number = 10 // 十进制
let a2: number = 0b1010  // 二进制
let a3: number = 0o12 // 八进制
let a4: number = 0xa // 十六进制
```

### 3. 字符串

```ts
let name:string = 'tom'
name = 'jack'
// name = 12 // error
let age:number = 12
const info = `My name is ${name}, I am ${age} years old!`
```

