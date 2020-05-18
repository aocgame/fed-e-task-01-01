#### 1. 请说出下列最终的执行结果，并解释

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6]();
```

结果为 10

分析：变量 `i` 是通过 `var` 进行声明的，其所处作用域是全局作用域，`a[6]` 中 `function` 内部的 `i` 指向的是全局的 `i`，当循环结果，执行 `a[6]()` 时，`i` 的值为 10



#### 2. 请说出下列最终的执行结果，并解释

```javascript
var tmp = 123;

if (true) {
  console.log(tmp);
  let tmp
}
```

结果：会报引用错误

分析：let 声明的变量 tmp 不可在声明之前引用，在 let 之前会存在 TDZ 即“暂时性死区”，如果在 TDZ 中引用 let 声明的变量，会报“ReferenceError”，引用错误



#### 3. 结合 ES6 新语法，用最简单的方式找出数组中的最小值

```javascript
var arr = [12, 34, 32, 89, 4];
```

结果：`Math.min(...arr)` 得到 4



#### 4. 请详细说明 `var`、`let`、`const` 三种声明变量的方式之间的具体差别



#### 5. 请说出下列代码最终输出的结果，并解释为什么

```javascript
var a = 10;
var obj = {
  a: 20,
  fn () {
    setTimeout(() => {
      console.log(this.a);
    })
  }
}
obj.fn()
```

结果：20

分析：一般情况下，setTimeout 中的回调函数执行时，内部的 this 都指向 window，但此时 setTimeout 中的回调函数是箭头函数，箭头函数没有“自己”的 this，内部的 this 就是指 fn () {...} 中的 this，所以，当执行 obj.fn() 时，fn () {...} 中的 this 指向 obj，this.a 即 obj.a 



#### 6. 简述 `Symbol` 类型的用途



#### 7. 说说什么是浅拷贝，什么是深拷贝



#### 8. 谈谈你是如何理解 JS 异步编程的，`Event Loop` 是做什么的，什么是宏任务，什么是微任务



#### 9. 将下面异步代码使用 `Promise` 改进

```javascript
setTimeout(function () {
  var a = "hello";
  setTimeout(function () {
    var b = "lagou";
    setTimeout(function () {
      var c = "I ❤️ U";
      console.log(a + b + c);
    }, 10);
  }, 10);
}, 10);
```

答案：

```javascript
// case 1
Promise.resolve("hello").then((res) => `${res}lagou`).then(res => {
  console.log(`${res}I ❤️ U`);
});

// case 2
new Promise((resolve, reject) => {
  resolve("hello");
}).then(res => {
  return `${res}lagou`;
}).then(res => {
  console.log(`${res}I ❤️ U`)
})
```





#### 10. 请简述 `TypeScript` 与 `Javascript` 之间的关系



#### 11. 请谈谈你所认为的 `TypeScript` 优缺点
