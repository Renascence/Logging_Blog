---
title: 浅析react diff的机制
date: 2019-08-25 23:15:51
tags:
---

一、 基本概念

## Real DOM：浏览器中真实存在的节点。

![Real DOM](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/RealDom.png)

Virtual dom：使用js描述Real DOM的对象。

![Virtual DOM](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/VirtualDom.png)

Virtual dom 转化为 Real DOM：

![Virtual DOM -> Real DOM](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/v2r.png)

## 二、 为什么要使用Virtual DOM

  从浏览器的角度来说，js执行肯定没有直接渲染html快才对，那我们为什么需要v-dom呢。先从浏览器的工作原说起：

![浏览器工作原理](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/browser.png)


Repaint（重绘）：元素产生了不影响排版的情况下后对这个元素进行重新绘制的过程。

Reflow（重排）：元素产生了对文档树排版有影响的样式变化，对所有受影响的dom节点进行重新排版的过程。

提高网页性能，就是要降低"重排"和"重绘"的频率和成本，尽量少触发重新渲染。这就是v-dom帮我们完成的事情。
（Tips：所以一些对dom操作不多对页面，还是尽量使用html编写，或者对速度有要求的页面可以通过ssr实现效果。）

浏览器对于重排和重绘的优化：
```
  div.style.color = 'red';
  div.style.marginTop = '10px' // 浏览器会合并执行，只触发一次重绘和重排
```

浏览器无法优化的情况：
```
  div.style.color = 'red';
  const margin = parseInt(div.style.marginTop); // 无法优化，立即进行一次重绘
  div.style.marginTop = '10px'
```

面对浏览器无法优化的情况，我们就需要v-dom的帮助。

## 三、 React diff策略

- tree diff：Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。

![tree diff](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/treeDiff.png)

- component diff：拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构

![component diff](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/componentDiff.png)

- element diff：对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。

  - INSERT_MARKUP，新的 component 类型不在老集合里， 即是全新的节点，需要对新节点执行插入操作。 
  - MOVE_EXISTING，在老集合有新 component 类型，且 element 是可更新的类型，generateComponentChildren 已调用 receiveComponent，这种情况下 prevChild=nextChild，就需要做移动操作，可以复用以前的 DOM 节点。 
  - REMOVE_NODE，老 component 类型，在新集合里也有，但对应的 element 不同则不能直接复用和更新，需要执行删除操作，或者老 component 不在新集合里的，也需要执行删除操作。

![domChange](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/domChange.png)

![domDIff](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/domDIff.png)
