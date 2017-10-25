---
title: es6 新特性之set/map
date: 2017-5-15 23:31:24
tags: es6
---

>  最近一直在使用es6的新特性，但是还没有系统地学习，写博客开始慢慢补漏～  

## [](#Set "Set")Set

  es6提供的一种新数据结构。

  和数组类似，但是中间不允许存在重复的值。

  区别于数组的push，向set集合中添加元素需要使用add方法。  
  获取长度使用set.size()函数，而不是length。  
  set.has()函数用于判断集合中是否含有某一个元素。

  * 数组和集合的转化：  

    ```
      let array = Array.from(sets);
    ```

  集合没有特定的key，所以访问‘键’和‘值’获取到的数据是一致的。

## [](#Map "Map")Map

  和object类似，但是传入的key不需要是string类型。

  * 基本方法：

    ```
      size    // 返回成员总数
      set(key,val)
      get(key)
      hsa(key)     
      delete(key)  
      clear  // 清空map
    ```

  **只有对同一个对象的引用，Map结构才将其视为同一个键。**

  ```
    var map = new Map();
    map.set(['a'], 555);
    map.get(['a']) // undefined

  ```
  **同样的值的两个实例，在Map结构中被视为两个键。**

  *   Map转为数组
    ```
      let arr = [...myMap]
    ```

  *   遍历map
    ```
      for (let [k,v] of strMap) {
        // do something with key &val
      }
    ```