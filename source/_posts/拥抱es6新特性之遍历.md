---
title: 拥抱es6新特性之遍历
date: 2017-6-26 12:02:31
tags: es6
---

> 以前一直使用for语法遍历array/object，es6提供了一种更优雅的方式遍历array/object。整理一下最近学习的array的原型方法。

# [](#Array "Array")Array

## [](#map "map")map

  语法：  
  ```
    arr.map(function(currentVal,index,arr) {})
  ```

  > 该方法会返回一个新的数组，值为callback函数返回的每一项。

## [](#reduce "reduce")reduce

  语法：  

  ```
    arr.reduce(function(val1,val2) {
    },[initialValue])
  ```

该方法会遍历数组，val1代表initialValue或者上一次执行callback的返回值，val2代表数组遍历的当前元素，通常用来求数组的和。

## [](#every "every")every

  语法：  

  ```
    arr.every(callback[, thisArg])
  ```

  > 对数组的每个元素进行检查，所有元素都满足callback函数的条件，最终才返回true，否则返回值为false。

## [](#some "some")some

  语法：  
  ```
    arr.some(callback[, thisArg])
  ```

  和every对应，只要数组中有一个或者多个满足callback条件，则返回true，否则返回false。

## [](#fill "fill")fill

  语法：  

  ```
    arr.fill(value)
    arr.fill(value, start)
    arr.fill(value, start, end) // start和and为可选
  ```

  对数组进行填充，可以指定填充的开始位置和结束位置。
  很好用的一个函数，之前一直用for循环给数组赋初始值。

## [](#filter "filter")filter

  语法：  

  ```
    arr.filter(callback[, thisArg])
  ```

  该方法会返回一个新的满足callback函数条件的数组。

## [](#find "find")find

  语法：  
  ```
    arr.find(callback[, thisArg])
  ```

  返回数组中满足callback函数条件的第一个元素的值，如果不存在则返回undefined。

## [](#findIndex "findIndex")findIndex

  语法：  
  ```
    arr.findIndex(callback[, thisArg])
  ```

  和indexOf()方法类似，返回第一个满足callback条件的元素下标，如果不存在则返回-1.

## [](#forEach "forEach")forEach

  语法：  
  ```
    array.forEach(callback(currentValue, index, array){},this)
  ```

  遍历数组，对数组中的每个元素进行callback中的操作。

## [](#keys "keys")keys

  语法：  

  ```
    arr.keys()
  ```

  返回新的arr迭代器，包含数组中所有index。**包括空元素的索引**。