---
title: react 中的key
date: 2017-7-18 16:32:54
tags: react 前端
---

  react提供了强大的jsx语法，其中我使用最多的就是三目表达式以及map方法渲染array元素了。

  react动态渲染array：
    ```
      array.map(item => <li>item</li>)

    ```

  这么写console里会出现warning：

    > Each child in an array or iterator should have a unique "key" prop. 

  提示的错误很明显嘛，动态渲染的元素需要唯一的key值。遂改成
    ```
      array.map((item,index) => <li key={index}>item</li>)

    ```
  简直完美，页面正常渲染，console中也不会报错。



-------------


  很久之前到最近我都是这么认为的。
  直到有一天我接到了一个需求：根据数组长度动态生成输入框，并且输入框支持删除添加操作。

  拿起键盘就是一把梭：
  ```
    // 几个主要函数  state = {list : []}

    onInputChange = (e, index) => {
      const list = this.state.list.slice();
      list[index] = e.target.value;
      this.setState({ list });
    }

    onInputRemove = (index) => {
      const list = this.state.list.slice();
      list.splice(index, 1)
      this.setState({ list });
    }

    onInputAdd = () => {
      const list = this.state.list.slice();
      list.push(null)
      this.setState({ list });
    }

    render() {
      return <div>
        {this.state.list.map((item, index) => <div key={index}>
          <input onChange={(e) => this.onInputChange(e,index)} defaultValue={item} />
          <button onClick={() => this.onInputRemove(index)}>删除</button>
        </div>)}
        <button onClick={() => this.onInputAdd()}>添加</button>
      </div>
    }
  ```
  ![添加了四个输入框](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/before.png)

  主要功能就这么实现了，逻辑堪称完美。
  直到我点击了一下删除按钮。。。发现我删除的永远是最后一个元素（图为点击第二个输入框后删除按钮出现的效果）

  ![点击第二个输入框后面的删除按钮后](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/after.png)
  
  第一反应是我删除的index删错了，但是反复检查之后，发现逻辑并没有问题，于是研究了一番。
  BTW：当时是用value替换了defaultvalue，让input变为受控组件，解决了这个问题。

  赶快Google一波补补课。

  >简单来说:react利用key来识别组件，它是一种身份标识标识。react的diff算法需要根据key的对比来实现。
  > * key相同，若组件属性有所变化，则react只更新组件对应的属性；没有变化则不更新。
  > * key值不同，则react先销毁该组件(有状态组件的componentWillUnmount会执行)，然后重新创建该组件（有状态组件的constructor和componentWillUnmount都会执行）
  也就是说，如果是同一个元素，如果需要更新状态，则赋予的key值不应该改变。

  根据搜到的解释以及页面上出现的现象，我理解如下：
  当删除了第二个输入框，通过map渲染出来的输入框 defaultValue={3} ，但是，由于通过数组map的index没有改变，都是1，所以react没有对view进行更新。// 所以让input变为受控组件，也可以解决这个问题。

  所以如果想通过key来解决这个问题，需要固定唯一的key就可以。
  ```
    constructor(props) {
      super(props)
      this.state = {
        list: [],
        keys: [0],
      }
    }

    onInputChange = (e, index) => {
      const list = this.state.list.slice();
      list[index] = e.target.value;
      this.setState({ list });
    }

    onInputRemove = (index) => {
      const list = this.state.list.slice();
      const keys = this.state.keys.slice();
      list.splice(index, 1);
      const order = keys.indexOf(index)
      keys.splice(order, 1)
      this.setState({ list, keys });
    }

    onInputAdd = () => {
      const list = this.state.list.slice();
      const keys = this.state.keys.slice();
      keys.push(keys[keys.length - 1] + 1)
      list.push(null)
      this.setState({ list, keys });
    }

    render() {
      const keys = this.state.keys;
      return <div>
        {this.state.list.map((item, index) => <div key={keys[index]}>
          <input onChange={(e) => this.onInputChange(e, index)} defaultValue={item} />
          <button onClick={() => this.onInputRemove(index)}>删除</button>
        </div>)}
        <button onClick={() => this.onInputAdd()}>添加</button>
      </div>
    }
  ```

  多维护一个数组，作为动态渲染的数组的key就可以解决。