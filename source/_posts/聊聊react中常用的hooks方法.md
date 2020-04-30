---
title: 聊聊react中常用的hooks方法
date: 2020-04-30 15:23:00
tags:
---

使用react 16也有一年多了，总结一下其中一些常用的hooks方法。
hooks使用在函数组件中，让函数组件也有了状态

## useState

>useState用法——声明、读取、使用（修改）

    const [ count , setCount ] = useState(0);
    //ES6的数组解构
    //接受状态初始值，返回数组（0：是数组状态值，1是改变函数的方法） 
    let _useState = userState(0);
    let count = _useState[0]
    let setCount = _useState[1]

> 多状态声明注意事项
> >useState 只是赋值，并没有绑定key值，如何让React保证可以找到对应的渲染的状态呢？
> >
> >答： 根据useState出现的顺序进行确定，不能出现在条件判断语句中，因为它必须要有完全一致的渲染顺序。

## useEffect

>生命周期函数的作用
>>答：和函数业务主逻辑关联不大，实现在特定时间或事件中执行的动作，比如Ajax请求，登录或取消登录的时间监听

> `useEffect`两个注意点
>>1. React首次渲染和之后的每次渲染都会调用一遍useEffect函数，而之前用的命周期函数分别表示首次渲染(componentDidMonut)和更新导致的重新渲染(componentDidUpdate)都是区分开来的。
>>2. useEffect中定义的函数的执行不会阻碍浏览器更新视图异步执行，而componentDidMonut和componentDidUpdate中的代码都是同步执行的。有好处也有坏处，比如根据页面的大小绘制当前弹出窗口大小，如果时异步的就不好操作了。

## useContext
useContext可以帮助实现跨越组件层级直接传递变量，实现数据的共享。    
useContext和redux的作用不同，一个解决组件值传递，一个是应用中统一管理状态问题。    
useContext + useReducer 可以实现Redux作用
Context的作用就是对包含在其中的组件实现全局数据共享的作用    

## useReducer
useReducer可以让代码更具有可读性和可维护性   
Reducer其实就是一个函数，这个函数接受两个参数，一个是状态，另一个用来控制业务逻辑的判断参数，控制状态的作用

## useMemo
useMemo主要用来解决React Hooks产生的无用渲染的性能问题。在使用function的形式来声明组件，失去了shouldComponentUpdate这个生命周期，决定组件是否更新。已不再区分mount和update两个状态。

## useCallback
useCallback是为了缓存方法 useMemo是为了缓存变量,避免组件重复执行时，申明多次函数/变量，减小开销。