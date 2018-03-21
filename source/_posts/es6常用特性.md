---
title: es6常用特性
date: 2018-01-30 22:18:31
tags: es6
---

使用es6语法开发也有一年多了，总结一下es6中常用的一些技巧。

## 1. 解构与展开运算符

```
  const student = {
    name: 'xiaoming',
    id: 1,
    age: 23,
  }
```

如果在es5中想去除student中的变量，需要这样

```
  const name = student.name, id=student.id, age=student.age
```

这样的代码过于繁琐，我们可以使用es6的解构语法来优化它。

```
  const {name , age, id} = student
```

展开运算符和解构结合使用也能简化部分代码，比如student这个对象中有3个属性值，现在需要其中除去name剩下的2个字段组成新的对象，可以这么做

```
  const {name, ...rest} = student
  rest // {age: 23, id:1} 注意：此操作为深拷贝
```

展开运算符在开发中使用最多的还是要数redux了，不使用展开运算符：

```
  function todoApp(state = initialState, action) {
    switch (action.type) {
      case SET_VISIBILITY_FILTER:
        return Object.assign({}, state, {
          visibilityFilter: action.filter
        })
      default:
        return state
    }
  }
```

使用后：

```
  function todoApp(state = initialState, action) {
    switch (action.type) {
      case SET_VISIBILITY_FILTER:
        return { ...state, visibilityFilter: action.filter }
      default:
        return state
    }
  }
```

替换了Object.assign方法，代码更简洁。

小技巧：使用解构交换两个变量的值:
```
  [a,b] = [b,a]
```
最早看到这种操作方式是在Python中，现在es6也支持了。
第一次看到这个代码惊为天人，省去了中间变量的声明，把三行代码缩减至一行，确实是很实用的功能。

## 2. arrow funtion

箭头函数简化了function语法，同时解决了this指向问题。

```
  function Person() {
    this.age = 0;
    setInterval(function() {
      this.age // 非严格模式指向window对象
    }, 1000);
  }
```
在箭头函数出现之前我们一般这么解决问题:
```
  function Person() {
    var that = this;
    that.age = 0;
    setInterval(function() {
      that.age++; // 在外部申明变量指向this，在函数内部调用
    }, 1000);
  }
```
借助箭头函数，我们可以这样写：
```
  function Person(){
    this.age = 0;
    setInterval(() => {
      this.age++;
    }, 1000);
  }
```
另外，箭头函数中的this不会被call/bind改变指向。

箭头函数不绑定arguments对象。
```
  const arguments = 42;
  const arr = () => arguments; 
  arr() // 42 全部变量 可以使用剩余参数方式访问传入参数
```
## 3. 函数默认参数

在es5中，对传入参数判断一般这样写：
```
  function multiply(a,b) {
    b = (typeof b !== 'undefined') ?  b : 1;
    return a * b;
  }
```
借助默认参数，可以改写成：
```
  function multiply(a, b = 1) { // 默认参数
    return a * b;
  }
```

## 4. Promise

Promise 解决了es5中回调地狱的问题，让代码逻辑变得更加直观。

```
  const promise = new Promise((resolve,reject)=> {
    setTimeout(() => { // 创建异步任务
      if(data) {
        resolve(data) // 正常返回数据
      } else {
        reject(err) // 异常
      }
    }, 1000);
  })

  // 指定回调函数
  promise.then(res => {
    console.log(res)  // 监听成功的回调
  }).catch(err => {
    console.log(err)  // 监听错误的回调
  })
```

为了让代码看上去更简洁，通常还会使用es7规范中的async/await语法（通过webpakck插件转译）。

```
  async function getData(url){
    return fetch(url)
  }

  try {
    const data = await getData(url)
  } catch(err) {
    console.log(err)
  }

  // 如果有多个互不依赖的请求可以使用promise.all提升效率
  const [res1,res2,res3] = await Promise.all([getData1,getData2,getData3])
```

## 5. fetch

在过去浏览器发起请求都是ajax,现在可以使用新的api fetch。
准确来说 fetch 应该属于es7的标准,但是大部分浏览器都已经支持,而且这个特性我使用比较多很方便,所以一起列出来。
fetch返回的是promise对象,可以直接使用then和catch方法

```
  // 默认为get 
  fetch(url)
  .then((response) => {
    console.log(response)
  })
  .catch((err) => {
    console.log(err)
  });

  // 设置post请求参数
  const option = {
    method: 'POST',
    body: {},
  }
  fetch(url, option)
  .then((response) => {
    console.log(response)
  })
  .catch((err) => {
    console.log(err)
  });
```

注意: 
- Fetch 请求默认是不带 cookie 的，需要设置 fetch(url, {credentials: 'include'})
- 服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。


## 6. const 和 let

在es5中，只有函数作用域和全局作用域的概念，es6中新引入了块级作用域的概念。
```
  {
    let a = 1;
  }
  a // Uncaught ReferenceError: a is not defined
```
var申明的变量存在变量提升，const和let不存在。

对于简单类型，使用const 定义常量，使用let 定义变量。

对于复杂类型来说，都应该使用const 定义。

使用const声明的复杂类型保证了变量的类型不会改变,但是不能保证复杂元素中的值不被改变。
```
  const a = {b:1}
  a = 1  // Uncaught TypeError: Assignment to constant variable.
  a.b = 2
  a //  {b:2}
```
