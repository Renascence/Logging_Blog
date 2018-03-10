---
title: react-router v4 code split
date: 2018-03-10 20:38:44
tags: react 
---

## 为什么需要code split

现在越来越多的网页应用都变成了spa，spa的用户体验自然是比传统网页好。但是随着项目越来越大，而spa又是一次性将全部代码下载至本地，所以用户在首屏浏览的等待时间可能会过长，这时候我们就需要使用到代码分割（code split）。代码分割可以在用户跳转页面时根据路由加载对应页面的文件，这样就减小了首次下载的文件体积，缩短白屏时间。

## bundle-loader

这是webpack官方用来分割代码的插件，在现有项目中使用这个插件需要如下几步操作：

1. 安装（废话。。。）

```
  npm i bundle-loader --save
```

2. 在webpack中配置插件

```
  module: {
    rules: [{
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel-loader', // 添加bundle-loader
      query: {
        plugins: [
          ['import', [{ libraryName: "antd", style: true }]],
        ]
      }
    }, {
      test: /\.css$/,
      loader: 'style-loader!css-loader'
    }, {
      test: /\.less$/,
      use: [{
        loader: "style-loader"
      }, {
        loader: "css-loader"
      }, {
        loader: "less-loader"
      }]
    }],
  },
```

3. 插件的使用


我们先需要定义一个组件

```
import React, { Component } from 'react';

class Bundle extends Component {
  constructor() {
    super()
    this.state = {
      mod: null
    }
  }

  componentDidMount() {
    this.props.load((mod) => {
      this.setState({
        mod: mod.default || mod
      })
    })
  }

  render() {
    return (
      this.state.mod ? this.props.children(this.state.mod) : null
    )
  }
}

export default Bundle;

```

然后在路由组件中给我们需要分割的组件做如下操作：

```
import Bundle from './components/bundle/Bundle';
 <div>
  <Route
    path="/"
    exact
    render={() => (
      <Bundle load={require('bundle-loader?lazy!./components/home/Home')}>
        {Home => <Home />}
      </Bundle>)
    }
  />
   <Route
    path="/movieDetail/:id"
    exact
    render={props => ( // 如果需要route组件提供相关的props则需要传入此参数
      <Bundle load={require('bundle-loader?lazy!./components/movieDetail/MovieDetail')}>
        {MovieDetail => <MovieDetail {...props} />}
      </Bundle>)
    }
  />
  </div>
```

接下来执行
```
  npm run build 
```
就可以看到代码成功被分割的情况啦～