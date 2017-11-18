---
title: js模块化中的CommonJS规范
date: 2017-11-18 22:42:25
tags: javascript
---

说起js的模块化，现在很方便的联想到require/import等方式，然而最初js是没有模块这个概念的，不同js文件之间的相互引用依赖于在html中通过 script 标签引入文件，才能相互使用其中的方法。后来随着技术发展，缺少模块化的js使用实在不便，于是模块化规范诞生了。


### CommonJS

  CommonJS定义了js模块之间相互引用的规范（主要是非浏览器环境中，node.js采用的就是此规范）。CommonJS规定每个模块内部，module变量代表当前模块。这个变量是一个对象，它的exports属性（即module.exports）是对外的接口。加载某个模块，其实是加载该模块的module.exports属性。

  CommonJS 加载模块是同步的，只有加载完成才能执行后面的操作。像Node.js依赖的模块基本都是存在硬盘上，加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。

  CommonJS模块导入导出使用 module.exports 和 require 方法，经过包装后的模块相当于
  ```
  function(exports, require, module, __filename, __dirname){
    // code
  }
  ```

  可以看出，经过以上的包装方式，所有在文件内部声明的变量如果没有被放在 exports 对象中，其他文件是无法获取到该变量的，如果想获取该变量，需要通过如下方式声明一个全局变量：
  ```
  global.attr = true;
  ```

  所有模块都是Module的实例。module具有以下基本属性
  - module.id 模块的识别符，通常是带有绝对路径的模块文件名。
  - module.filename 模块的文件名，带有绝对路径。
  - module.loaded 返回一个布尔值，表示模块是否已经完成加载。
  - module.parent 返回一个对象，表示调用该模块的模块。
  - module.children 返回一个数组，表示该模块要用到的其他模块。
  - module.exports 表示模块对外输出的值。

  // TODO 突然想起之前公司有个老司机提出过‘两个模块相互引用会不会死锁’的问题，带我研究一番回来继续填坑。
  