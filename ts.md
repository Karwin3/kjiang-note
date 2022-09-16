https://24kcs.github.io/vue3_study/

## 1. 初识ts

### 1. ts 安装

```i
npm install -g typescript
tsc -V
```



### 2. vscode 自动编译

```json
// 生成配置文件tsconfig.json
tsc --init

// 修改 tsconfig.json 配置
"outDir":".js",   // 编译文件输出目录
"strict":false    // 严格模式

// 启动监视任务
终端 => 运行任务 => 监视tsconfig.json
```



### 3. 类型注解

给 person 函数的参数添加 : string 类型注解，如下

```ts
function greeter (person: string) {
  return 'Hello, ' + person
}

let user = 'Yee'

console.log(greeter(user))
```



### 4. 接口

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



### 5. 类

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

### 6. webpack 打包 ts

#### 1. 下载依赖

```
    "clean-webpack-plugin": "^4.0.0",
    "cross-env": "^7.0.3",
    "html-webpack-plugin": "^4.5.0",
    "ts-loader": "^8.0.11",
    "typescript": "^4.0.5",
    "webpack": "^4.41.5",
    "webpack-cli": "^3.3.10",
    "webpack-dev-server": "^3.10.2"
```

```text
npm install -D typescript@4.0.5
npm install -D webpack@4.41.5 webpack-cli@3.3.10
// 启动开发服务器
npm install -D webpack-dev-server@3.10.2
// 打包html文件   清楚打包好的文件
npm install -D html-webpack-plugin clean-webpack-plugin
// 将 TypeScript编译成 JavaScript
npm install -D ts-loader@8.0.11
// 跨平台脚本
npm install -D cross-env
```

#### 2.入口JS: src/main.ts

```typescript
// import './01_helloworld'

document.write('Hello Webpack TS!')
```

#### 3. index页面: public/index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>webpack & TS</title>
</head>
<body>
  
</body>
</html>
```

#### 4. build/webpack.config.js

```javascript
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')

const isProd = process.env.NODE_ENV === 'production' // 是否生产环境

function resolve (dir) {
  return path.resolve(__dirname, '..', dir)
}

module.exports = {
  mode: isProd ? 'production' : 'development',
  entry: {
    app: './src/main.ts'
  },

  output: {
    path: resolve('dist'),
    filename: '[name].[contenthash:8].js'
  },

  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        include: [resolve('src')]
      }
    ]
  },

  plugins: [
    new CleanWebpackPlugin({
    }),

    new HtmlWebpackPlugin({
      template: './public/index.html'
    })
  ],

  resolve: {
    extensions: ['.ts', '.tsx', '.js']
  },

  devtool: isProd ? 'cheap-module-source-map' : 'cheap-module-eval-source-map',

  devServer: {
    host: 'localhost', // 主机名
    stats: 'errors-only', // 打包日志输出输出错误信息
    port: 8081,
    open: true
  },
}
```



## 2. ts 常用语法

### 1. 基础类型

#### 1. 布尔值

```ts
let isDone: boolean = false;
isDone = true;
// isDone = 2 // error
```

#### 2. 数字

```ts
let a1: number = 10 // 十进制
let a2: number = 0b1010  // 二进制
let a3: number = 0o12 // 八进制
let a4: number = 0xa // 十六进制
```

#### 3. 字符串

```ts
let name:string = 'tom'
name = 'jack'
// name = 12 // error
let age:number = 12
const info = `My name is ${name}, I am ${age} years old!`
```

#### 4. 数组

```ts
  let list1: number[] = [1, 2, 3]
  let list2: Array<number> = [1, 2, 3]
```

#### 5. 元组

// 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同，长度固定，类型匹配

```ts
  let t1: [string, number]
  t1 = ['hello', 10] // OK
  // t1 = [10, 'hello'] // Error
```

#### 6. 枚举

```ts
enum Color {
  Red,
  Green,
  Blue
}

// 枚举数值默认从0开始依次递增
// 根据特定的名称得到对应的枚举数值
let myColor: Color = Color.Green  // 0
console.log(myColor, Color.Red, Color.Blue)
```

默认情况下，从 0 开始为元素编号。 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从1 开始编号：

```typescript
enum Color {Red = 1, Green, Blue}
let c: Color = Color.Green
```

或者，全部都采用手动赋值：

```typescript
enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green
```

枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为 2，但是不确定它映射到 Color 里的哪个名字，我们可以查找相应的名字：

```typescript
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2]

console.log(colorName)  // 'Green'
```

#### 7. any

可以赋值任何数据类型

```typescript
let notSure: any = 4
notSure = 'maybe a string'
notSure = false // 也可以是个 boolean

let list: any[] = [1, true, 'free']
list[1] = 100
```

```ts
// 缺点 类似情况无报错信息
// list[0].split('')
```

#### 8. void 

 函数声明时写在函数后面，表示没有返回数据

```ts
 function voidfun(): void {
    console.log(1)
  }
```

#### 9. object

```ts
 // object 表示非原始类型，也就是除 number，string，boolean之外的类型。
  function fn2(obj:object):object {
    console.log('fn2()', obj)
    return {}
    // return undefined
    // return null
  }
  console.log(fn2(new String('abc')))
  // console.log(fn2('abc') // error
  console.log(fn2(String))
```

#### 10. 联合类型

   定义一个函数得到一个数字或字符串值的字符串形式值
   函数中的方法需要把数据类型都要照顾到，否则会报错

```ts
  function toString2(x: number | string): string {
    return x.toString()
  }
  console.log(toString2(123))
```

#### 11. 类型断言

类型断言(Type Assertion): 可以用来手动指定一个值的类型
语法:
    方式一: <类型>值
    方式二: 值 as 类型  tsx中只能用这种方式

```ts
  /* 需求: 定义一个函数得到一个字符串或者数值数据的长度 */
  function getLength(x: number | string) {
    if ((<string>x).length) {
      return (x as string).length
    } else {
      return x.toString().length
    }
  }
```

#### 12.类型推断

类型推断: TS会在没有明确的指定类型的时候推测出一个类型

```ts
/* 定义变量时赋值了, 推断为对应的类型 */
let b9 = 123 // number
// b9 = 'abc' // error

/* 定义变量时没有赋值, 推断为any类型 */
let b10  // any类型
b10 = 123
b10 = 'abc'
```



### 2. 接口

在 TypeScript 中，我们使用接口（Interfaces）来定义对象的类型

接口: 是对象的状态(属性)和行为(方法)的抽象(描述)

接口类型的对象

  多了或者少了属性是不允许的

  可选属性: ?

  只读属性: readonly



需求: 创建人的对象, 需要对人的属性进行一定的约束

 id是number类型, 必须有, 只读的

 name是string类型, 必须有

 age是number类型, 必须有

 sex是string类型, 可以没有

```ts
(() => {
  interface IPerson {
    readonly id: number
    name: string
    age: number
    sex?: string
  }

  const person2: IPerson = {
    id: 1,
    name: 'tom',
    age: 20,
    // sex: '男' // 可以没有
  }
})()
```

#### 1. 函数类型

接口能够描述 JavaScript 中对象拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以描述函数类型。

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。

```typescript
/* 
接口可以描述函数类型(参数的类型与返回的类型)
*/

interface SearchFunc {
  (source: string, subString: string): boolean
}
```

这样定义后，我们可以像使用其它接口一样使用这个函数类型的接口。 下例展示了如何创建一个函数类型的变量，并将一个同类型的函数赋值给这个变量。

```typescript
const mySearch: SearchFunc = function (source: string, sub: string): boolean {
  return source.search(sub) > -1
}

console.log(mySearch('abcd', 'bc'))
```

#### 2. 类类型

1. 一个类可以实现多个接口
2. 一个接口可以继承多个接口

```ts
;(() => {
  interface Alarm {
    alert(): any
  }

  interface Light {
    lightOn(): void
    lightOff(): void
  }

  class Car implements Alarm {
    alert() {
      console.log('Car alert')
    }
  }

  // 1. 一个类可以实现多个接口
  class Car2 implements Alarm, Light {
    alert() {
      console.log('Car alert')
    }
    lightOn() {
      console.log('Car light on')
    }
    lightOff() {
      console.log('Car light off')
    }
  }

  // 2. 一个接口可以继承多个接口
  interface LightableAlarm extends Alarm, Light {}

  class Car3 implements LightableAlarm {
    alert() {
      console.log('Car alert')
    }
    lightOn() {
      console.log('Car light on')
    }
    lightOff() {
      console.log('Car light off')
    }
  }
```

### 3.类

 类:模板，实例化对象

#### 1. 基本实例

```ts
;(() => {
  // 类的基本定义与使用

  class Greeter {
    // 声明属性
    message: string

    // 构造方法
    constructor(message: string = 'test') {
      this.message = message
    }

    // 一般方法
    greet(): string {
      return 'Hello ' + this.message
    }
  }

  // 创建类的实例
  const greeter = new Greeter('world')
  // 调用实例的方法
  console.log(greeter.greet())
})()
```

#### 2. 继承

```ts
// 继承
;(() => {
  // 父类
  class Person {
    name: string
    age: number
    constructor(name: string, age: number) {
      this.name = name
      this.age = age
    }
    sayHi(): string {
      return `hello,I'm ${this.name}`
    }
  }
  // 子类
  class Student extends Person {
    score: number
    constructor(name: string, age: number, score: number) {
      super(name, age)
      this.score = score
    }
    // 重写sayHi方法
    sayHi(): string {
      // 调用父类中的sayHi方法
      console.log(super.sayHi())

      return `hello,I'm ${this.name},I get ${this.score}`
    }
  }

  // 实例化对象
  const student = new Student('jk', 12, 100)
  console.log(student.sayHi())
})()

```

#### 3. 多态

```ts
// 多态
// const str: 父类 = new 子类
;(() => {
  // 父类
  class Person {
    name: string
    age: number
    constructor(name: string, age: number) {
      this.name = name
      this.age = age
    }
    sayHi(): string {
      return `hello,I'm ${this.name}`
    }
  }
  // 子类
  class Student extends Person {
    score: number
    constructor(name: string, age: number, score: number) {
      super(name, age)
      this.score = score
    }
    // 重写sayHi方法
    sayHi(): string {
      // 调用父类中的sayHi方法
      console.log(super.sayHi())

      return `hello,I'm ${this.name},I get ${this.score}`
    }
  }
  const student: Person = new Student('jk', 1, 100)
  // 调用的方法为子类中重写的方法
  console.log(student.sayHi())
})()

```

#### 4. 修饰符

 访问修饰符: 用来描述类内部的属性/方法的可访问性

 public: 默认值, 公开的外部也可以访问

 private: 只能类内部可以访问

 protected: 类内部和子类可以访问

#### 5. readonly 修饰符

你可以使用 `readonly` 关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```typescript
class Person {
  readonly name: string = 'abc'
  constructor(name: string) {
    this.name = name
  }
}

let john = new Person('John')
// john.name = 'peter' // error
```



在上面的例子中，我们必须在 `Person` 类里定义一个只读成员 `name` 和一个参数为 `name` 的构造函数，并且立刻将 `name` 的值赋给 `this.name`，这种情况经常会遇到。 参数属性可以方便地让我们在一个地方定义并初始化一个成员。 下面的例子是对之前 `Person` 类的修改版，使用了参数属性：

```typescript
class Person2 {
  constructor(readonly name: string) {
  }
}

const p = new Person2('jack')
console.log(p.name)
```

注意看我们是如何舍弃参数 `name`，仅在构造函数里使用 `readonly name: string` 参数来创建和初始化 `name` 成员。 我们把声明和赋值合并至一处。

#### 6. 存取器

TypeScript 支持通过 getters/setters 来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。

```ts
class Person{
    //定义内部name属性
    private _name;
    
    //在构造函数中对name属性进行初始化
    constructor(){
        this._name=""
    }

    //变量取用函数get
    get name(){
        return this._name;
    }

    //变量设置函数set
    set name(value:string){
        if(value.length<2||value.length>5){
            throw new Error("名字不合法，请重新输入")
        }
        this._name=value;
    }
}

//在类的外部调用存取器进行变量的读取和修改
let people=new Person();
//当我们访问类中的name变量时，会自动调用get方法
let personName=people.name;
console.log(people.name); //控制台输出 ： ""
//当我们为变量赋予新的值时，会自动调用set方法
people.name="jk";
console.log(people.name);  //控制台输出 ： jk
```
#### 7. 静态属性

```ts
/* 
静态属性, 是类对象的属性
非静态属性, 是类的实例对象的属性
*/

;(() => {
  class Person {
    name1: string
    static name2: string = 'B'
    constructor(name1: string) {
      this.name1 = name1
    }
  }

  console.log(Person.name2)
  console.log(new Person('jk').name1)
})()
```

#### 8. 抽象类

```ts
// 抽象类做为其它派生类的基类使用。 它们不能被实例化。
;(() => {
  abstract class Animal {
    abstract cry(): any

    run() {
      console.log('run()')
    }
  }

  class Dog extends Animal {
    cry() {
      console.log('Dog cry()')
    }
  }

  const dog = new Dog()
  dog.cry()
  dog.run()
})()

```

### 4. 函数

#### 1. 基本实例

```ts
  // 1. 基本实例
  // 命名函数
  function add(x: number, y: number): number {
    return x + y
  }

  // 匿名函数
  let myAdd = function (x: number, y: number): number {
    return x + y
  }

  // 2. 完整类型
  let myAdd2: (x: number, y: number) => number = function (
    x: number,
    y: number
  ): number {
    return x + y
  }
```

#### 2. 可选参数和默认参数

```ts
;(() => {
  function buildName(firstName: string = 'A', lastName?: string): string {
    if (lastName) {
      return firstName + '-' + lastName
    } else {
      return firstName
    }
  }

  console.log(buildName('C', 'D'))
  console.log(buildName('C'))
  console.log(buildName())
})()

```

#### 3. 剩余参数

```ts
;(() => {
  function info(x: string, ...args: string[]) {
    console.log(x, args)
  }
  info('abc', 'c', 'b', 'a')
})()
```

####  4. 函数重载

 函数重载: 函数名相同, 而形参不同的多个函数

```ts
// 需求: 我们有一个add函数，它可以接收2个string类型的参数进行拼接，也可以接收2个number类型的参数进行相加
;(() => {
  // 重载函数声明
  function add(x: string, y: string): string
  function add(x: number, y: number): number

  // 定义函数实现
  function add(x: string | number, y: string | number) {
    // 在实现上我们要注意严格判断两个参数的类型是否相等，而不能简单的写一个 x + y
    if (typeof x === 'string' && typeof y === 'string') {
      return x + y
    } else if (typeof x === 'number' && typeof y === 'number') {
      return x + y
    }
  }

  console.log(add(1, 2))
  console.log(add('a', 'b'))
  // console.log(add(1, 'a')) // error
})()
```
### 5. 泛型

#### 1. 基本实例

```ts
//需求: 根据指定的数量 count 和数据 value , 创建一个包含 count 个 value 的数组  
 function createArray2<T>(value: T, count: number) {
    const arr: Array<T> = []
    for (let index = 0; index < count; index++) {
      arr.push(value)
    }
    return arr
  }

  const arr3 = createArray2<number>(11, 3)

  console.log(arr3[0].toFixed())
  // console.log(arr3[0].split('')) // error

  const arr4 = createArray2<string>('aa', 3)
  
  console.log(arr4[0].split(''))
  // console.log(arr4[0].toFixed()) // error
```

#### 2. 多个泛型参数的函数

```ts
 function swap<K, V>(a: K, b: V): [K, V] {
    return [a, b]
  }
  const result = swap<string, number>('abc', 123)
  console.log(result[0].length)
  console.log(result[1].toFixed())
```

#### 3. 泛型接口

```ts
// 泛型接口
;(() => {
  interface IbaseCRUD<T> {
    data: T[]
    add: (t: T) => void
    getById: (id: number) => T | undefined
  }

  class User {
    id?: number //id主键自增
    name: string //姓名
    age: number //年龄

    constructor(name: string, age: number) {
      this.name = name
      this.age = age
    }
  }

  class UserCRUD implements IbaseCRUD<User> {
    data: User[] = []

    add(user: User) {
      user = { ...user, id: Date.now() }
      this.data.push(user)
      console.log('保存user', user.id)
    }

    getById(id: number) {
      return this.data.find((item) => item.id === id)
    }
  }

  const userCRUD = new UserCRUD()
  userCRUD.add(new User('tom', 12))
  userCRUD.add(new User('tom2', 13))
  console.log(userCRUD.data)
})()

```

#### 4. 泛型类

```ts
;(() => {
  class GenericNumber<T> {
    zeroValue!: T
    add!: ((x: T, y: T) => T)
  }

  let myGenericNumber = new GenericNumber<number>()
  myGenericNumber.zeroValue = 0
  myGenericNumber.add = function (x, y) {
    return x + y
  }

  let myGenericString = new GenericNumber<string>()
  myGenericString.zeroValue = 'abc'
  myGenericString.add = function (x, y) {
    return x + y
  }

  console.log(myGenericString.add(myGenericString.zeroValue, 'test'))
  console.log(myGenericNumber.add(myGenericNumber.zeroValue, 12))
})()

```

#### 5. 泛型约束

```ts
;(() => {
  interface Lengthwise {
    length: number
  }

  // 指定泛型约束
  function fn2<T extends Lengthwise>(x: T): void {
    console.log(x.length)
  }
  // fn2(123) // error
  fn2('123')
})()

```

