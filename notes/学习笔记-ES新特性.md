### ES 新特性

#### let 与块级作用域

ES2015 之前只有全局作用域和函数作用域，ES2015 新增了块级作用域

let 用于循环中需要注意的点：

```javascript
for (let i = 0; i < 3; i++) {
  let i = 'foo';
  console.log(i);
}

// 上述循环拆解开每一轮循环，如下
let i = 0;

if (i < 3) {
  let i = 'foo';
  console.log(i);
}

i++;

if (i < 3) {
  let i = 'foo';
  console.log(i);
}

i++;

if (i < 3) {
  let i = 'foo';
  console.log(i);
}

i++;
```

循环体中的作用域与块级作用域的关系

普通的 var 声明变量，会发生声明提升，变量的声明会被提升至当前作用域的顶部，因此，在 var 声明语句出现之前引用相关变量不会报错；

let 声明变量，会在声明语句之前形成 TDZ，即“暂时性死区”，限定变量不可在声明语句之前被引用，否则会报引用错误



#### const

const 声明用于声明常量、恒量，其在 let 基础上增加了“只读”特性，声明过后不允许被修改，且声明时就必须初始化，声明和赋值不能放在两个语句中

注意：不可被修改是指，声明的常量不能重新指向一个新的内存地址，常量中的属性成员还是可以被修改的

```javascript
const obj = {};
obj.name = 'hello'; // pass

obj = {}; // error
```



> 最佳实践：不用 var，主用 const，配合 let



#### 数组的解构

简述为：根据位置，一一对应，对应赋值

```javasc
const arr = [100, 200, 300];

const [foo, bar, baz] = arr;
console.log(foo, bar, baz); // 100, 200, 300

// 提取某一个值
const [, , baz] = arr;
console.log(baz); // 300

// 提取一串值，配合...符号，只能在解构位置最后一个成员上使用
const [foo, ...rest] = arr;
console.log(rest); // [200, 300]

const [foo, bar, baz, more] = arr;
console.log(more); // undefined 一一对应

// 默认值
const [foo, bar, baz = 123, more = 'default value'] = arr;
console.log(bar, more); // 300, default value
```



#### 对象的解构

简述为：根据名字，一一对应，对应赋值

```javascript
const obj = { name: 'xiaoming', age: 18 };

const { name } = obj;
console.log(name); // xiaoming

// 默认值
const { age = 19 } = obj;
console.log(age); // 18
const { gender } = obj;
console.log(gender); // undefined

// 设置别名重命名，避免冲突 默认值
const name = 'tom';
const { name: objName = 'jack' } = obj;
console.log(objName); // xiaoming
```



#### 模板字符串

反引号 ``` 使得字符串内换行不需要手动添加换行符，字符串的拼接不仅仅依靠连字符号，通过插值表达式增强字符的拼接

```javascript
const name = 'tom';
const msg = `hey, ${name}`;
console.log(msg); // hey, tom
```

#### 带标签的模板字符串

给模板字符串前添加一个标签函数

```javascript
const name = 'tom';
const gender = true;

function myTagFunc (strings, name, gender) {
  console.log(strings); // ['hey,', ' is a ', '.'] strings 为模板字符串中依据插值表达式分割后的字符串数组
  console.log(name); // tome
  console.log(gender); // true
  return strings[0] + name + strings[1] + gender + strings[2]; // 可以自定义输出，作为模板字符串的返回结果
}

const result = myTagFunc`hey, ${name} is a ${gender}.`;

console.log(result); // hey, tom is a true.
```

标签函数的作用在于，对模板字符串加工，增强模板的输出



#### 字符串的拓展方法

```javascript
const msg = 'Error: foo is not defined.'
// includes()
msg.includes('foo'); // true 简化使用 indexOf 或 正则查找的步骤
// startsWith()
msg.startsWith('Error'); // true
// endsWith()
msg.endsWith('.'); // true
```



#### 参数默认值

```javascript
// 简化在函数内部对参数进行校验
function foo (enable = true) {
  console.log('foo invoked - enable: ');
  console.log(enable);
}

foo(false);

// 如果有多个参数，带有默认值的参数只能排在参数列表最后
function bar (baz, enable = true) {
  console.log(enable);
}

bar('hello', false);
```



#### 剩余参数

```javascript
// ES6 之前，要获取函数不定个数的参数时，要通过函数内部的类数组对象 arguments 来实现
// ES6 通过展开操作符获取参数
function foo (...args) {
  console.log(args); // 1, 2, 3, 4
}

foo(1, 2, 3, 4);

// 注意！带有展开操作符的参数只能放置在形参列表中的末尾，且只能出现一次
function bar (first, ...args) {
  console.log(first); // 1
  console.log(args); // [2, 3, 4]
}

bar(1, 2, 3, 4);

// 另外 ...作为展开操作符，也可用于展开数组
const arr = ['foo', 'bar', 'baz'];
// ES6 之前打印 arr
console.log.apply(console, arr); // foo bar baz
// 通过展开操作符
console.log(...arr); // foo bar baz
```



#### 箭头函数

最主要的作用是简化函数的编写

```javascript
function inc (number) {
  return number + 1
}

// 箭头函数
const inc = n => n + 1 // 方法体只有 return 语句时，可以省略 {} 和 return 关键字
const incc = n => {
  console.log('inc invoked');
  return n + 1; // 如果方法体不仅仅只有 return 语句，{} 和 return 关键字都不能省略，返回值需要手动通过 return 关键字返回
}

// 另外，箭头函数不会改变 this 的指向，箭头函数中的 this 是箭头函数所处的当前作用域中的 this
const person = {
  name: 'tom',
  sayHi: function () {
    console.log(`hi, my name is ${this.name}`)
  },
  // 注意形式，不要与 function 的简化书写混淆
  sayHello: () => {
    console.log(`hi, my name is ${this.name}`)
  },
  sayHiAsync: function() {
    const _this = this
    setTimeout(function() {
      console.log(this.name);
      console.log(_this.name);
    }, 1000)
  },
  sayHelloAsync: function() {
    setTimeout(() => {
      console.log(this.name)
    }, 1000)
  }
}

person.sayHi(); // hi, my name is tom
person.sayHello(); // hi, my name is undefined
person.sayHiAsync();
// undefined setTimeout 回调函数的 this 默认是指向全局作用域中的 window 或 global
// tom
person.sayHelloAsync(); // tom
```



#### 对象字面量的增强

```javascript
// 属性名与属性值的简写
const bar = '345'

const obj = {
  foo: 123,
  // bar: bar
  bar, // 当属性值是一个变量，且变量名与当前属性名一致时，可简写
  // method1: function() {
  //   console.log('method1111')
  //   console.log(this)
  // }
  // 注意此处方法的简写，需要与箭头函数区分开
  method1 () {
    console.log('method1111')
    console.log(this)
  }
}

// 计算属性名
const person = {
  name: 'tom',
  // 可以通过表达式的返回值，动态的设置属性名，不再需要声明变量后，手动通过索引的方式设置
  // person[Math.random()] = 123
  [Math.random()]: 123
  // [key]: value 动态的 key
}

// 注意一点，对象的属性名，都是字符串！
```



#### Object.assign

将多个源对象中的属性复制到一个目标对象中

```javascript
const source1 = {
  a: 123,
  b: 123
}

const target = {
  a: 456,
  c: 456
}

const result = Object.assign(target, source1)

console.log(target) // { a: 123, c: 456, b: 123 }
console.log(result === target) // true Object.assign 的返回值就是复制操作后的目标对象
```



#### Object.is

Object.is 主要用于处理跟数学有关的问题，绝大多数情况下，`===` 可以用于判断两个操作数是否全等，例如

```javascript
0 == false // true
0 === false // false
```

但是，对于 `+0` 和 `-0`

```javascript
+0 === -0 // true
```

新的同值比较：

```javascript
Object.is(+0, -0) // false
Object.is(NaN, NaN) // true 对 NaN 的认识在更新
```



#### Proxy VS Object.defineProperty()

defindProperty 只能监视属性的读写

Proxy 能够监视到更多对象操作，例如 delete 操作、对对象方法调用的操作

```javascript
const person = {
  name: 'tom',
  age: 20
}

const personProxy = new Proxy(person, {
  deleteProperty (target, property) {
    console.log('delete', property)
    delete target[property]
  }
})
// 监视 delete 操作
delete personProxy.age // delete age
console.log(person) // { name: 'tom' }
```

Proxy 更好的支持数组对象的监视，以往的实现是通过重写数组的操作方法

```javascript
const list = []
// 监视数组的写入操作
const listProxy = new Proxy(list, {
  set (target, property, value) {
    console.log('set', property, value)
    target[property] = value
    return true // 表示设置成功
  }
})

listProxy.push(100)
// set 0 100
// set length 1
```

Proxy 是以非侵入的方式监管了对象的读写





#### Reflect

是一个静态类，可以看做是 `Math`，例如 `Reflect.get()`

Reflect 内部封装了一系列对对象的底层操作

Reflect 成员方法就是 Proxy 处理对象的默认实现

```javascript
const obj = {
  foo: '123',
  bar: '456'
}

const proxy = new Proxy(obj, {
  get (target, property) {
    return Reflect.get(target, property) // 可以理解为，Reflect 是 Proxy 实现监听的默认操作
  }
})
```

作用在于统一提供了一套用于操作对象的 API

```javascript
const obj = {
  name: 'tom',
  age: 18
}

// 以往
console.log('name' in obj) // true
console.log(delete obj['age']) // true
console.log(Object.keys(obj)) // [ 'name' ]

// 现在
console.log(Reflect.has(obj, 'name')) // true
console.log(Reflect.deleteProperty(obj, 'age')) // true
console.log(Reflect.ownKeys(obj)) // true
```



#### 类

实例方法 vs 静态方法

```javascript
class Person {
  constructor (name) {
    this.name = name
  }
  
  say () {
    console.log(`hi, my name is ${this.name}`)
  }
  // 静态方法
  static create(name) {
    return new Person(name)
  }
}
// 调用静态方法
const tom = Person.create('tom')
tom.say() // hi, my name is tome
// 注意！静态方法是挂载到类上的，因此，静态方法内部的 this 不会指向实例上，而是当前类上
```



类的继承

```javascript
class Person {
  constructor (name) {
    this.name = name
  }
  
  say () {
    console.log(`hi, my name is ${this.name}`)
  }
}

class Student extends Person {
  constructor (name, number) {
    super(name) // super 永远指向父类，调用父类构造函数对成员进行初始化
    this.number = number
  }
  
  hello () {
    super.say() // 调用父类的方法
    console.log(`my school number is ${this.number}`)
  }
}

const s = new Student('jack', '100')
s.hello()
// hi, my name is jack
// my school number is 100
```



#### Set 数据结构

一种全新的“集合”，其成员各不相同

```javascript
const s = new Set()

s.add(1).add(2).add(3).add(4).add(2) // 操作方法会返回自身，支持链式调用
console.log(s) // Set { 1, 2, 3, 4}

// 对集合进行遍历
s.forEach(i => console.log(i))
// 1
// 2
// 3
// 4

for (let i of s) {
  console.log(i)
}
// 1
// 2
// 3
// 4

console.log(s.size) // 4 整个集合的长度

console.log(s.has(100)) // false

console.log(s.delete(3)) // true
console.log(s) // Set { 1, 2, 4}

s.clear() // 清除 Set 中的全部内容
console.log(s) // Set {}

// 用途：常用于数组去重
const arr = [1, 2, 1, 3, 4, 1]

let result = Array.from(new Set(arr))
// 或者
result = [...new Set(arr)]
// 需要将 Set 数据结构转换为 数组

console.log(result) // [1, 2, 3, 4]
```



#### Map 数据结构

Object 一般来说，对象的属性名都是字符串，Map 可以看做是 Object 的拓展

严格意义上的键值对集合，用于映射两个任意类型数据之间的对应关系

```javascript
const m = new Map()
const tom = { name: 'tom' }
m.set(tom, 90)
console.log(m) // Map { { name: 'tome' } => 90 }

console.log(m.get(tom)) // 90

// m.has()
// m.delete()
// m.clear()

// 遍历
m.forEach((value, key) => {
  console.log(value, key)
})
// 90 { name: 'tom' }
```



#### Symbol

原始类型，表示独一无二的值

```javascript
const s = Symbol()
console.log(s) // Symbol()
console.log(typeof s) // symbol

console.log(Symbol() === Symbol()) // false 独一无二

// 添加字符串作为描述文本
console.log(Symbol('foo')) // Symbol(foo)
console.log(Symbol('bar')) // Symbol(bar)
console.log(Symbol('baz')) // Symbol(baz)

// 自此，对象的属性名也可以设置为 Symbol 类型
const obj = {
  [Symbol()]: 123
}

console.log(obj) // { [Symbol()]: 123 }

// 模拟实现对象的私有成员
// a 文件，只对外暴露 say 方法
const name = Symbol()
const person = {
  [name]: 'tom',
  say () {
    console.log(this[name])
  }
}
// b 文件
person.say() // tom 此时，b 文件是无法访问 person 对象的 [name]
```

另外，如果要复用一个 Symbol 类型的值

```javascript
const s1 = Symbol.for('foo')
const s2 = Symbol.for('foo')
console.log(s1 === s2) // true
// 注意！for 方法维护的是 字符串与 Symbol 之间的对应关系，如果传入的不是字符串，会自动转换为字符串
console.log(Symbol(true) === Symbol('true')) // true
```

实现自定义 `toString` 标签

```javascript
const obj = {
  [Symbol.toStringTag]: 'XObject'
}
console.log(obj.toString()) // [object XObject]
```

> 用 Symbol 类型为对象设置的属性名，是无法通过 `for in`、`Object.keys` 获取的，通过 JSON.stringify 进行序列化时，相关属性也会被自动忽略

如何获取对象中 Symbol 类型的属性名？

```javascript
const obj = {
  [Symbol()]: 'symbol value',
  foo: 'normal value'
}
console.log(Object.getOwnPropertySymbol(obj)) // [ Symbol() ]
```



#### for...of 循环

已有的几种循环：

1. for 适合遍历数组
2. for...in 适合遍历键值对的集合
3. 一些对象的遍历方法，例如 Array.prototype.forEach

上述遍历方式都有一定的局限性，ES6 推出的 for...of 循环，作为遍历所有数据结构的统一方式

```javascript
const arr = [100, 200, 300, 400]

// 对数组遍历，得到每一个元素，而不是下标
for (const item of arr) {
  console.log(item)
}
// 100
// 200
// 300
// 400

// 可以取代 arr.forEach()，并且
// arr.forEach() 不能跳出循环
// 以往若要终止循环，需要使用
// arr.some()
// arr.every()

// for...of 可以使用 break 关键字终止循环
for (const item of arr) {
  console.log(item)
  if (item > 100) {
    break
  }
}
// 100
// 200

// 伪数组对象也可以使用 for...of 循环进行遍历，例如 arguments 和 DOM 中的元素节点列表

// 对 Set 数据结构遍历
const s = new Set(['foo', 'bar'])

for (const item of s) {
  console.log(item)
}
// foo
// bar

// 对 Map 数据结构遍历，遍历得到的每一项是键和值组成的数组
const m = new Map()
m.set('foo', '123')
m.set('bar', '345')

for (const item of m) {
  console.log(item)
}
// [ 'foo', '123' ]
// [ 'bar', '345' ]

// 对 Map 数据结构遍历时利用数组解构
for (const [key, value] of m) {
  console.log(key, value)
}
// foo 123
// bar 345


// 注意！如果尝试用 for...of 循环对普通对象循环，会报错
const obj = { foo: 123, bar: 456 }
for (const item of obj) {
  console.log(item)
}
// TypeError: obj is not iterable
// 此时的 obj 不是可迭代的，不可用 for...of 遍历
```



#### 可迭代接口

以数组为例

```javascript
const arr = ['foo', 'bar', 'baz']
const iterator = arr[Symbol.iterator]()

console.log(iterator.next()) // { value: 'foo', done: false }
console.log(iterator.next()) // { value: 'bar', done: false }
console.log(iterator.next()) // { value: 'baz', done: false }
console.log(iterator.next()) // { value: undefined, done: true }

// for...of 循环对数组遍历，相当于在其内部执行 iterator.next()，并返回对应的值
// 数组内部这个 Symbol.iterator 方法，可以返回一个带有 next() 方法的对象，
// Symbol.iterator 便是可迭代接口
```

以 Set 为例

```javascript
const set = new Set(['foo', 'bar', 'baz'])
const iterator = set[Symbol.iterator]()

console.log(iterator.next()) // { value: 'foo', done: false }
console.log(iterator.next()) // { value: 'bar', done: false }
console.log(iterator.next()) // { value: 'baz', done: false }
```

以 Map 为例

```javascript
const m = new Map()
m.set('foo', '123')
m.set('bar', '345')
// Map(2) {"foo" => "123", "bar" => "345"}
const iterator = m[Symbol.iterator]()

console.log(iterator.next()) // { value: ["foo", "123"], done: false }
console.log(iterator.next()) // { value: ["bar", "345"], done: false }
console.log(iterator.next()) // { value: undefined, done: true }
```



##### 对 obj 实现可迭代接口

```javascript
const obj = { // 实现可迭代接口 Iterable
  [Symbol.iterator]: function() {
    return { // 实现迭代器接口 Iterator
      next: function() {
        return { // 实现迭代结果 IterationResult
          value: 'tom',
          done: true
        }
      }
    }
  }
}

for (const item of obj) {
  console.log('循环体')
}
// 无打印内容，因为 done: true，表明迭代器终止

// 升级
const obj = {
  store: ['foo', 'bar', 'baz'],
  
  [Symbol.iterator]: function() {
    let index = 0
    const self = this
    
    return {
      next: function() {
        const result = {
          value: self.store[index],
          done: index >= self.store.length
        }
        index++
        return result
      }
    }
  }
}

for (const item of obj) {
  console.log('循环体', item)
}
// 循环体 foo
// 循环体 bar
// 循环体 baz
```



##### 迭代器模式

对外提供统一遍历接口

```javascript
const todos = {
  life: ['吃饭', '睡觉', '打豆豆'],
  learn: ['语文', '数学', '外语'],
  work: ['喝茶'],
  
  each: function (callback) {
    const all = [].concat(this.life, this.learn, this.work)
    for (const item of all) {
      callback(item)
    }
  },
  
  [Symbol.iterator]: function() {
    const all = [...this.life, ...this.learn, ...this.work]
    let index = 0
    return {
      next: function () {
        return {
          value: all[index],
          done: index++ >= all.length
        }
      }
    }
  }
}

// old 冗余代码太多，且 todos 内部数据结构如有更改，外部遍历方法又需要重写
for (const item of todos.life) {
  console.log(item)
}
for (const item of todos.learn) {
  console.log(item)
}
for (const item of todos.work) {
  console.log(item)
}
// 对 todos 的数据遍历
todos.each(function (item) {
  console.log(item)
})

// now 无需关注内部数据结构
for (const item of todos) {
  console.log(item)
}
```



#### 生成器

特定：惰性执行

```javascript
function * foo () {
  console.log('1111')
  yield 100
  console.log('2222')
  yield 200
  console.log('3333')
  yield 300
}

const generator = foo()

console.log(generator.next())
// 1111
// { value: 100, done: false }
console.log(generator.next())
// 2222
// { value: 200, done: false }
console.log(generator.next())
// 3333
// { value: 300, done: false }
console.log(generator.next())
// { value: undefined, done: true }
```

##### 生成器应用

```javascript
// 发号器
function * createIdMaker () {
  let id = 1
  while (true) {
    yield id++
  }
}

const idMaker = createIdMaker()

console.log(idMaker.next().value) // 1
console.log(idMaker.next().value) // 2
console.log(idMaker.next().value) // 3
console.log(idMaker.next().value) // 4

// 使用 Generator 实现 iterator 方法
const todos = {
  life: ['吃饭', '睡觉', '打豆豆'],
  learn: ['语文', '数学', '外语'],
  work: ['喝茶'],
  
  [Symbol.iterator]: function * () {
    const all = [...this.life, ...this.learn, ...this.work]
    for (const item of all) {
      yield item
    }
  }
}

for (const item of todos) {
  console.log(item)
}

// 最主要的用途在于，解决异步编程过程中，回调嵌套过深的问题
```



#### ES2016 概述

新增了 Array.prototype.includes，indexOf 不能查找数组中的 NaN，includes 可以

指数运算符

```javascript
// old
console.log(Math.pow(2, 10)) // 1024
// now
console.log(2 ** 10) // 1024
```

#### ES2017 概述

##### Object 对象的三个扩展方法

```javascript
const obj = {
  foo: 'value1',
  bar: 'value2'
}

// Object.values 与 Object.keys 相对，返回属性值组成的数组
console.log(Object.values(obj)) // [ 'value1', 'value2' ]

// Object.entries 返回键值对数组组成的数组
console.log(Object.entries(obj)) // [['foo', 'value1'], ['bar', 'value2']]
for (const [key, value] of Object.entries(obj)) {
  console.log(key, value)
}
// foo value1
// bar value2

// 对象转 Map
console.log(new Map(Object.entries(obj)))
// Map {'foo' => 'value1', 'bar' => 'value2'}

// Object.getOwnPropertyDescriptiors 获取属性的完整描述信息

// 例如 Object.assign 只能复制对象的普通属性
const p1 = {
  firstName: 'Lei',
  lastName: 'Wang',
  get fullName () {
    return this.firstName + ' ' + this.lastName
  }
}

const p2 = Object.assign({}, p1) // 只能复制普通属性，无法复制 getter 
p2.firstName = 'Lin'
console.log(p2.fullName) // Lei Wang

const descriptors = Object.getOwnPropertyDescriptors(p1)
const p3 = Object.defineProperties({}, descriptors)
p3.firstName = 'Lin'
console.log(p3.fullName) // Lin Wang
```

##### 新增2个字符串填充方法

用给定字符串填充目标字符串的开始、结束位置，直到达到指定长度为止，格式化输出

String.prototype.padStart、String.prototype.padEnd

```javascript
const books = {
  html: 5,
  css: 16,
  javascript: 128
}

for (const [name, count] of Object.entries(books)) {
  console.log(`${name.padEnd(16, '-')}|${count.toString().padStart(3, '0')}`)
}
// html------------|005
// css-------------|016
// javascript------|128
```

##### 允许在函数参数中添加尾逗号

```javascript
function foo (bar, baz,) {}
```

##### Async / Await

