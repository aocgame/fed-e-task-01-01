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

