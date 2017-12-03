---
title: js模块化中的AMD和CMD规范
date: 2017-12-03 23:30:11
tags:
---

上篇文章中说了在node环境中，各个模块基本上都是已经存储在硬盘上可以直接调用，但是在浏览器环境下，各个模块加载的时间和顺序都不一样，CommonJS是不适用的，所以适用于浏览器环境的模块规范 AMD 和 CMD 就诞生了。

### AMD

AMD模块推荐使用写法

```
define("alpha", ["require", "exports", "beta"], function (require, exports, beta) {
  exports.verb = function() {
    return beta.verb();
    return require("beta").verb();
  }
});
```

在define函数中，第一个参数指定了当前模块的id，第二个参数指定了该模块需要用到哪些依赖，第三个参数为模块本身。

### CMD

CMD推荐写法

```
define(id, function(require, exports, module) {
  // 获取模块 a 的接口
  var a = require('./a');

  // 调用模块 a 的方法
  a.doSomething();

  exports.doSomething = function() {};
});
```

第一个参数是本模块的id，第二个参数为一个函数，里面需要指明依赖的模块和导出的内容。

通过以上代码，我们可以看出两者使用方式的区别，AMD需要提前声明依赖的模块，而CMD则是依赖就近，使用前才声明。

虽然RequireJS（AMD）和SeaJS（CMD）在使用时不使用推荐写法也能工作，但是为了避免出现奇怪的问题（例如执行顺序），最好还是按照推荐写法实现。

### 我在项目中常用的模块机制

现在我们使用的最多的开发环境就是webpack + babel了，在开发时，通常直接使用import，但是为了考虑浏览器的兼容情况，babel通常把模块转换为CommonJS规范的模块。


参考链接：

[AMD模块规范](https://github.com/amdjs/amdjs-api/wiki/AMD)
[CMD模块规范](https://github.com/seajs/seajs/issues/242)
