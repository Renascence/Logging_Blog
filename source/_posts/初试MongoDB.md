---
title: 初试MongoDB
date: 2017-04-25 22:40:08
tags: node 数据库
---

## [](#mongodb-简介 "mongodb 简介")mongodb 简介

  MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
  之前使用mysql比较多，mysql的某些术语在mongodb中有另外一个术语与之对应。

SQL术语/概念  | MongoDB术语/概念|解释/说明
------------|-------------|-----------
 database  |database|数据库
 table|collection|数据库表/集合
row|document|数据记录行/文档
column|field|数据字段/域
index|index| 索引
table joins|表连接,MongoDB不支持|-
primary key|primary key|主键,MongoDB自动将_id字段设置为主键

## [](#mongodb-安装 "mongodb 安装")mongodb 安装

操作系统：ubuntu 16.04

1.  导入包管理系器使用的公钥：
```
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
2.  为mongodb创建一个列表：
```
  echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee/etc/apt/sources.list.d/mongodb-org-3.4.list
```
3.  更新包管理区的软件列表：
```
    sudo apt-get update
```
4.  mongodb 安装：
```
    sudo apt-get install -y mongodb-org 
```


## [](#mongodb-服务控制 "mongodb 服务控制")mongodb 服务控制

1.  启动：
```
    sudo service mongod start
```
2.  查看mongodb 状态：
```
    sudo service mongod status
```

3.  停止：
```
    sudo service mongod stop
```

4.  重启：
```
    sudo service mongod restart
```

## [](#mongodb-常用操作 "mongodb 常用操作")mongodb 常用操作


1. 新建mongodb存储的文件路径
  ```
      mkdir -p /data/db
  ```

  /data/db 是 MongoDB 默认的启动的数据库路径(–dbpath),安装过程中不会自动创建，需要手动创建。

2. 执行 **mongo** 可以进入mongodb shell

  ```
      use DATABASE_NAME
  ```

  如果数据库不存在，则创建数据库，否则切换到指定数据库。

3. 如果需要删除数据库，在use 该数据库后执行

  ```
      db.dropDatabase()
  ```

  然后通过**show dbs**查看是否删除成功


## [](#在nodejs中使用mongodb "在nodejs中使用mongodb")在nodejs中使用mongodb

  因为主要是用nodejs进行开发，所以就介绍在nodejs平台使用mongodb了。

  首先需要通过npm安装mongodb

  ```
    npm install mongodb
  ```

  这个是官方提供的库，我们可以使用对其封装过之后的orm方便开发，在查阅资料之后，我选择了[mongous](https://github.com/amark/mongous),语法简洁，容易使用。

  ```
  npm install mongojs
  ```

  新建index.js文件：

```
  const mongojs = require('mongojs');
  const JSONStream = require('JSONStream') 
  const db = mongojs('logging', ['mycollection']) // logging 是数据库名 
  // save data 
  db.mycollection.save({ 
    name: 'logging', 
    age:24 
  }) 
  //get 
  db.mycollection.find((err,docs)=> { 
    console.log(docs) 
  })

  //通过id查找数据 并通过流输出到控制台 
  db.mycollection.find({_id: mongojs.ObjectId('58e9ebfa53d28433350c70e7')}).pipe(JSONStream.stringify()).pipe(process.stdout) 

  //增加条件查询语句 
  db.mycollection.find({"age" : {$gt : 100}}) 

  // update 
  db.mycollection.findAndModify({ 
    query: { name: 'logging' }, 
    update: { $set: { age: 23 }},  
    new: true 
  }, function (err, doc, lastErrorObject) { 
  }) 

  // delete 
  db.mycollection.remove({age:23}) 
```

  执行**node index.js **可以看到输出结果

## [](#参考 "参考")参考

[mongodb 文档](https://docs.mongodb.com/manual)  
[mongodb 教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)

