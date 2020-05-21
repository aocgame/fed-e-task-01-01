### TypeScript

---

#### 类型系统

##### 强类型与弱类型

- 强类型语言中不允许任意的隐式类型转换，而弱类型语言则允许任意的数据隐式类型转换
- 变量类型允许随时改变的特点，不是强弱类型的差异

##### 静态类型与动态类型

静态类型：一个变量声明时它的类型就是明确的，声明过后，它的类型就不允许再修改

动态类型：运行阶段才能够明确变量类型，而且变量的类型随时可以改变

> 动态类型语言中的变量是没有类型的，变量中存放的值是有类型的



##### 小结

弱类型的问题：

```javascript
// 错误提示不及时
const obj = {}
obj.foo() // 会立即报错

setTimeout(() => {
  obj.foo() // 不会理解报错
}, 1000000)

// 类型不确定，函数功能可能改变
function sum (a, b) {
  return a + b
}

console.log(sum(100, 100))
console.log(sum(100, '100')) // 输出结果可能与设计目的有出入

// 索引器的类型要求不明确，造成索引器错误的用法
const obj = {}
obj[true] = 100

console.log(obj['true'])
```

强类型的优势：

1. 错误能够更早的暴露
2. 代码更智能，编码更准确
3. 重构更牢靠
4. 减少不必要的类型判断



---

#### Flow

快速上手

```shell
# 安装
yarn add flow-bin --dev
```

使用

```javascript
// @flow

function sum (a: number, b: number) {
	return a + b
}

sum(100, 100)
sum('100', '100')
```

`yarn flow init` 创建 `.flowconfig` 配置文件

执行 `yarn flow` 监听，`yarn flow stop` 停止

> 注意！`flow` 的作用在于开发时对类型进行限制，代码运行前，需要将类型注解移除

##### 移除注解

- 使用官方的 `flow-remove-types` 工具

```shell
yarn add flow-remove-types --dev

# yarn flow-remove-types 源代码目录 -d 代码输出目录
yarn flow-remove-types src -d dist
```

- 通过 `babel` 配合插件

```shell
# install
yarn add @babel/core @babel/cli @babel/preset-flow --dev
```

创建 .babelrc

```json
{
  "preset": ["@babel/preset-flow"]
}
```

使用

```shell
yarn babel src -d dist
```



##### Flow 开发工具插件

`Flow Language Support` 插件，需要每次执行命令行获取类型校验结果

##### Flow 类型推断

会根据调用时传入的参数类型，推断代码中变量的类型

##### 类型注解

```javascript
/**
 * 类型注解
 * @flow
 */

function square (n: number) {
  return n * n
}

let num: number = 100

function foo (): number {
  return 'string' // 校验不通过，函数返回值应是 number 类型
}
// 变量冒号后为类型注解
```



---

#### Flow 具体类型

##### Flow 原始类型

```javascript
const a: string = 'foo'
const b: number = Infinity // NaN // 100
const c: boolean = false // true
const d: null = null
const e: void = undefined
const f: symbol = Symbol()
```

##### Flow 数组类型

```javascript
const arr1: Array<number> = [1, 2, 3, 4]
const arr2: number[] = [1, 2, 3, 4]
// 元组
const foo: [string, number] = ['foo', 100]
```

##### Flow 对象类型

```javascript
const obj1: { foo: string, bar: number } = { foo: 'string', bar: 100 }
const obj2: { foo?: string, bar: number } = { bar: 100 }

const obj3: { [string]: string } = {}
obj3.key1 = 'value1'
obj3.key2 = 'value2'
```

##### Flow 函数类型

```javascript
function foo (callback: (string, number) => void) {
  callback('string', 100)
}

foo(function (str, n) {
  // str => string
  // n => number
})
```

##### Flow 特殊类型

```javascript
// 字面量类型，常配合联合类型组合几个特定的值使用
const a: 'foo' = 'foo' // 类型和值都只能为该字面量
const type: 'success' | 'warning' | 'danger' = 'success'

// 联合类型、或类型
const b: string | number = 'string' // 100

type StringOrNumber = string | number
const c: StringOrNumber = 'stirng' // 100

// Maybe 类型，在具体类型之上，拓展了 null、undefined 的值
const gender: ?number = null // undefined
// 相当于
const gender: number | null | void = undefined
```

##### Flow Mixed 与 Any

```javascript
// Mixed 类型，可接受任意类型数据，相当于所有类型的联合类型
function passMixed (value: mixed) {
  
}

passMixed('string')
passMixed(100)

// Any 类型，也可接受任意类型数据
function passAny (value: any) {
  
}

passAny('string')
passAny(100)

// 二者区别
// mixed 是强类型，any 是弱类型
// 若在 passMixed 内部肆意执行
value.substr(1)
value * value
// 会报错，因为 mixed 仍是一个具体的类型，只能它能接受任意类型的数据，若要对其进行操作
if (typeof value === 'string') {
  value.substr(1)
}
if (typeof value === 'number') {
  value * value
}

// 而在 passAng 内部执行
value.substr(1)
value * value
// 不会报错

// any 只要是用于兼容老代码
```



#### Flow 类型小结

具体的所有类型详见 [官方文档](https://flow.org/en/docs/types/)

推荐 [类型手册](https://www.saltycrane.com/cheat-sheets/flow-type/latest)

##### Flow 运行环境 API

内置对象（JS core、dom、bom、cssom、node）

```javascript
// dom
const element: HTMLElement | null = document.getElementById('app')
```



---

#### TypeScript

##### 快速上手

```shell
# install
yarn add typescript --dev
```

在 ts 文件中

```javascript
// 可以完全按照 JavaScript 标准编写代码

const hello = (name: string) => {
  console.log(`Hello, ${name}`)
}

hello('TypeScript')
```

命令行中执行

```shell
yarn tsc index.ts
# 编译 ts 文件
# 检查类型使用异常
# 移除类型注解等扩展语法
# 自动转换 ES 新特性
```

在项目中使用 ts 配置文件

```shell
yarn tsc --init
# 生成 tsconfig.json 文件

# 直接运行
yarn tsc
# 自动寻找 tsconfig.json 并根据配置进行编译
```



---



#### 具体类型

##### 原始类型

```javascript
const a: string = 'foo'
const b: number = 100 // NaN Infinity
const c: boolean = true // false
// 与 Flow 不同，上述三种原始类型可以为空 null undefined（在 ts 非严格模式下）
// tsconfig.json 中 strict: false 或者 strictNullCheck: false
const d: boolean = null

const e: void = undefined // ts 非严格模式下 可以为 null
const f: null = null
const g: undefined = undefined

// 因为 Symbol 类型是 ES6 引入的
// ts 中若要使用 Symbol 类型，tsconfig.json 的 target 一项至少设置为 es2015
const h: symbol = Symbol()
```

##### 标准库声明

TS 针对每一个版本的 ES 都有相对应的标准库声明，使用相关特性则要考虑对应版本的支持，例如

ES5 中无法直接使用 Promise，es5 的 ts 文件无法引用 ES2015 的标准库！

- target 选项修改为对应版本，例如要使用 Promise，则修改为 es2015
- lib 选项指定要引用的标准库 ["ES2015", "dom"]，***记住，默认的标准库会被该项覆盖，及时检查是否有遗漏的标准库！***

##### 中文报错信息

settings 中 Typescript: locale 设置为 zh-CN

不推荐

##### 作用域问题

对多个 ts 单文件，不同文件中的同名变量可能会提示变量冲突，解决方法

- 将代码放入立即执行函数中 

```javascript
(function () {
  const a = 123
})()
```

- 使用 ES module 导出，在文件尾部编写 `export {}` 将整个文件转入模块作用域作为模块导出，避免变量提示冲突



##### Object 类型

并不单指普通对象类型，泛指所有非原始类型，对象、数组、函数

```javascript
export {}

const foo: object = function () {} // [] // {}
// 普通对象
const obj: {} = {}
// 要求，属性一一对应，不能多也不能少，
// 推荐使用 interface 定义普通对象
const obj1: { foo: number, bar: string } = { foo: 123, bar: 'string' }
```



##### 数组类型

```javascript
// 1. 使用 Array 泛型
const arr1: Array<number> = [1, 2, 3]

// 2.
const arr2: number[] = [1, 2, 3]

// example
function sum (...args: number[]) {
  return args.reduce((prev, current) => prev + current, 0)
}

sum(1, 2, 3, '4') // 报错
```

##### 元组类型

相当于数组类型的宽泛版本

```javascript
export {}
// 同样要求，对应位置类型一一对应，数量不多也不少
const tuple: [number, string] = [18, 'tom']

const age = tuple[0]
const name = tuple[1]
// or 解构提取
const [age, name] = tuple

Object.entries({
  foo: 123,
  bar: 456
})
// 得到的结果就是元组
```



##### 枚举类型

场景：需要用数值表示某种状态

```javascript
export {}

// old
const PostStatus = {
  Draft: 0,
  Unpublished: 1,
  Published: 2
}

// now
enum status = {
  Draft = 0,
  Unpublished = 1,
  Published: = 2
}
// 可以不指定值，默认是 0，自增
// 只指定第一个值，后续会在此基础上自增
// 字符串枚举无法自增，需要手动指定所有值

const post = {
  title: 'title',
  content: 'content',
  status: 2, // 1 // 0
  // 使用枚举值
  postStatus: status.Draft
}

```

***枚举类型会入侵到运行时的代码，会影响到结果***

```javascript
// 枚举类型会转变为双向的键值对对象，可以通过键获取值，可以通过值获取键
status[status["Draft"] = 0] = "Draft"
status[status["Unpublished"] = 1] = "Unpublished"
status[status["Published"] = 2] = "Published"

// 可通过索引器的方式获取枚举值
```

***常量枚举***

```javascript
const enum Status {
  Draft,
  Unpublished,
  Published
}

// 如果代码中确定不会通过索引器的方式获取枚举值时，
// 则不会生成双向键值对对象

// 编译后代码，使用的枚举会被移除，替换为原本具体的值，枚举的名称会以注释的方式出现
var post = {
  title: 'title',
  content: 'content',
  status: 0 /* Draft */ // 3 // 1 //0
}
```



##### 函数类型

- 函数声明

```javascript
function func1 (a: number, b: number): string {
  return 'func1'
}

// 形参实参个数、类型都要一致
func1(100, 200)
func1(100) // 报错
func1(100, 200, 300) // 报错

// 可选参数
function fun1 (a: number, b?: number): string {
  return 'func1'
}
// 添加参数默认值后，该参数自动变为可选参数
function fun1 (a: number, b: number = 10): string {
  return 'func1'
}
```

***可选参数、默认参数只能出现在参数列表末尾***

```javascript
// 接收任意个数参数
function func1 (a: number, b: number = 10, ...res: number[]): string {
  return 'func1'
}
// 不报错
func1(100, 200)
func1(100)
func1(100, 200, 300)
```

- 函数表达式

```javascript
const func2 = function (a: number, b: number): string {
  return 'func2'
}
// 一般来说会自动推断接收上述函数的变量 func2 的类型
// 作为回调函数使用时，需要手动指定，约束回调函数及参数的类型
const func2: (a: number, b: number) => string = function (a: number, b: number): string {
  return 'func2'
}
```



##### 任意类型 Any

```javascript
function stringify (value: any) {
  return JSON.stringify(value)
}
// 此时 value 应该支持任意类型，是动态类型
// any 类型不会做类型检查，可能存在类型安全问题
// 主要用于兼容老代码
```



##### 隐式类型推断

```javascript
let age = 18 // 隐式的推断为 number 类型

age = 'string' // 会报错

let foo // 推断为 any 类型

foo = 100
foo = 'string'

```

***建议为每个变量添加明确的类型***

##### 类型断言

某些情况下，TS 无法自动推断准确类型，开发者则可以手动断言明确类型

```javascript
const nums = [110, 120, 130, 140]
const res = nums.find(i => i > 0) // res: number | undefined TS 认为可能找不到指定元素
// 而开发者则明确知道能找到指定元素，并能够执行以下操作
const square = res * res // 如果没有类型断言，此处会报错，res 有可能是 undefined
```

- 使用 `as` 进行断言

```javascript
const num1 = res as number
```

- 使用 `<类型>`

```javascript
const num2 = <number>res // JSX 下不能使用
```

***目的：在多个可能存在的类型中，明确一个指定的类型（程序认为有多个可能，开发者很明确此处应该是指定类型的数据）***



##### TypeScript 接口

只是为有结构的数据做类型约束，约定对象的结构，编译过后，并不会出现在实际运行的代码中

```javascript
interface Post {
  title: string
  content: string
}

function printPost (post: Post) {
  console.log(post.title)
  console.log(post.content)
  // 此处要求 post 至少拥有 title、content 属性
}

printPost({
  title: 'this is title',
  content: 'this is content'
})
```

***一个对象要想实现一个接口，则必须拥有接口中约定的成员***

###### 可选成员

```javascript
interface Post {
  title: string
  subtitle?: string
  content: string
}

// => 相当于标记其类型 Post.subtitle: string | undefined
```

###### 只读成员

```javascript
interface Post {
  title: string
  readonly summary: string
}

const hello: Post = {
  title: 'Hello',
  summary: 'A summary'
}

hello.summary = 'other' // 报错，只读成员不可修改
```

###### 动态成员

```javascript
interface Cache {
  [key: string]: string
}

const cache: Cache = {}

cache.foo = 'value1'
cache.bar = 'value2'

// 常用于约定缓存对象
```



##### TypeScript 类

作用：描述一类具体事物的抽象特征，描述一类具体对象的抽象成员

ES6 以前，通过 函数+原型 模拟实现类

ES6 开始 JavaScript 中有专门的类

TypeScript 增强了 class 的相关语法（特殊的访问修饰符、抽象类）

```javascript
class Person {
  name: string // = 'init name' // 一般在构造函数中初始化
  age: number // 类的属性一定要有初始值，初始化不在此处，就在构造函数中
  constructor (name: string, age: number) {
    // 类的属性，在使用之前必须在类中先声明
    this.name = name
    this.age = age
  }

	sayHi (msg: string): void {
    console.log(`I am ${this.name}, ${msg}`)
  }
}
```

###### 访问修饰符

```javascript
// private
class Person {
  name: string
  private age: number // private 修饰的属性，私有属性，只能在内部访问
  protected gender: boolean
  
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
    this.gender = true
  }

	sayHi (msg: string): void {
    console.log(this.age)
  }
}

const tom = new Person('tom', 18)
console.log(tome.name)
console.log(tom.age) // 报错 实例对象无法访问私有属性

// 与 private 私有属性相对的，publice 修饰的是公有属性，是默认的

// protected
console.log(tom.gender) // 报错 无法访问 gender 属性

class Student extends Person {
  constructor (name: string, age: number) {
    super(name, age)
    console.log(this.gender) // 子类中访问 gender 属性不报错
  }
}

// protected 修饰的属性，只能在子类中访问到父类的该属性

```

> // 另外 构造函数的访问修饰符，默认是 public
> // 如果构造函数被设置为 private，则该类无法在外部通过 new 创建实例，也无法被继承
> // 如需创建该类的实例，需要在类的内部通过静态方法创建

```javascript
class Student extends Person {
  private constructor (name: string, age: number) {
    super(name, age)
    console.log(this.gender)
  }

  static create (name: string, age: number) {
    return new Student(name, age)
  }
}

const ming = Student.create('ming', 12)

```

> // 同样，如果构造函数被设置为 protected，则该类也无法在外部通过 new 创建实例，但是可以被继承



###### 类的只读属性

```javascript
class Person {
  public name: string
  privete age: number
  protected readonly gender: boolean // 只读属性
  
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
    this.gender = true // 只读属性再被初始化后，不管是在内部还是在外部，都不可修改
  }

	sayHi (msg: string): void {
    console.log(msg)
    console.log(this.age)
  }
}

const tom = new Person('tom', 18)
tom.gender = false // 报错

```



###### 类与接口

通过接口去约束类之间公共的能力，在类之上抽象出来的接口相较于父类更加抽象

```javascript
interface EatAndRun {
  eat (food: string): void
  run (distance: number): void
}

class Person implements EatAndRun {
  eat (food: string): void {
    console.log(food)
  }
  
  run (distance: number) {
    console.log(distance)
  }
}

class Animal implements EatAndRun {
  eat (food: string): void {
    console.log(food)
  }

	run (distance: number) {
    console.log(distance)
  }
}
```

> 每个接口的定义应该简单、细化

上述接口可以细化为：

```javascript
interface Eat {
  eat (food: string): void
}

interface Run {
  run (distance: number): void
}

class Person implements Eat, Run {
  eat (food: string): void {
    console.log(food)
  }
  run (distance: number) {
    console.log(distance)
  }
}
```



###### 抽象类

与接口类似，约束子类必须拥有某些成员；与接口不同的是，接口包括的是成员的抽象，不包含具体的实现，而抽象类包含实现

```javascript
abstract class Animal { // 抽象类只能被继承，不能用于创建实例对象
  eat (food: string): void {
    console.log(food)
  }

	abstract run (distance: number): void // 抽象方法
}

class Dog extends Animal {
  run (distance: number): void {
    console.log(distance)
  }
}

const d = new Dog()
d.eat('meat')
d.run(100)
```



###### 泛型

特征：定义函数、接口、类时不指定具体类型，使用时再指定类型，提高代码复用

```javascript
function createNumberArray (length: number, value: number): number[] {
  const arr = Array<number>(length).fill(value) // Array 对象默认创建的是 any 类型的数组，通过泛型参数<number>手动指定类型
  return
}

const res = createNumberArray(3, 100)
// res => [100, 100, 100]

```

> 如果泛型参数 number 不指定具体类型，而只是作为参数传递，则可提高上述代码复用性 

```javascript
function createArray<T> (length: number, value: T): number[] {
  const arr = Array<T>(length).fill(value) // 定义时，不明确的参数类型以 T 代指
  return arr
}

const res = createArray<string>(3, 'hello') // 使用时在指定具体类型，并将泛型参数传递进函数内部
// res => ['hello', 'hello', 'hello']
```

> 定义时不能够明确的类型变成一个参数，使用时再传递这个类型参数



###### 类型声明

开发过程中，有些代码不一定都是使用 TS 开发的，因此并不一定满足 TS 对于类型的限制要求

如果对其进行类型声明，则有助于在使用 TS 开发时，对其类型进行“补充说明”

使用第三方模块时，第三方模块不一定有类型声明，我们需要手动声明，以便代码兼容

```javascript
import { camelCase } from 'lodash'
// 对函数手动声明
declare function camelCase (input: string): string

const res = camelCase('hello typed') // 此时 camelCase 会有类型限制

```

对 lodash 库则有社区成熟的类型声明模块

```shell
# install 安装 lodash 类型声明模块
yarn add @types/lodash --dev
```

对其他模块，例如 query-string

```shell
yarn add query-string # 解析 url 中 query string
```

该模块中已经集成了类型声明文件，不需要再重复声明，使用时也会有对应的类型约束

[declare 拓展阅读](https://www.tslang.cn/docs/handbook/declaration-files/introduction.html)

[声明文件](https://ts.xcatliu.com/basics/declaration-files)

