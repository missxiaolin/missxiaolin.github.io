---
title: MongoDB安装
date: 2017-04-24 17:16:46
categories: "技术杂谈"
tags: ['php','linux']
---

### MongoDB 初识

MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

<img src="http://www.missxiaolin.com/mongodb-vs-mysql.png">
<img src="http://www.missxiaolin.com/Figure-1-Mapping-Table-to-Collection-1.png">

## 安装

[官方下载地址](https://www.mongodb.com/download-center)

安装步骤:
- [window平台安装 MongoDB](http://www.runoob.com/mongodb/mongodb-window-install.html)
- [Linux平台安装MongoDB](http://www.runoob.com/mongodb/mongodb-linux-install.html)

## 启动

启动MongoDB服务

~~~
mongod --dbpath=/path/to/mongodb
~~~

–dbpath 可选参数，如果不填写，则默认数据库文件路径为 /data/db

## MongoDB Shell

MongoDB Shell是MongoDB自带的交互式Javascript shell,用来对MongoDB进行操作和管理的交互式环境。
当你进入mongoDB后台后，它默认会链接到 test 文档（数据库）：

~~~
$ mongo
MongoDB shell version v3.4.1
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.1
Server has startup warnings:
2017-02-16T10:04:47.321+0800 I CONTROL  [initandlisten]
2017-02-16T10:04:47.321+0800 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2017-02-16T10:04:47.321+0800 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2017-02-16T10:04:47.321+0800 I CONTROL  [initandlisten]
>
~~~

## 数据库基本操作
~~~
> db //查看当前数据库
test
> show dbs //查看所有数据库
admin  0.000GB
local  0.000GB
test   0.000GB
> use local //切换数据库
switched to db local
> show collections //查询当前数据库所有表(集合).show tables也可以
col
message
post_total
~~~

## 数据表(集合)的操作

~~~
> db.message.find() //查看message表(集合)里的数据记录
{ "_id" : ObjectId("587ecec592434f4240668413"), "user_id" : 10 }
> db.message.insert({title: 'hello', content: 'Hello world!'}) //往message表(集合)里插入一条记录
WriteResult({ "nInserted" : 1 })
> db.message.find()
{ "_id" : ObjectId("587ecec592434f4240668413"), "user_id" : 10 }
{ "_id" : ObjectId("58a55ef57bc9a525061d0608"), "title" : "hello", "content" : "Hello world!" }
> db.message.remove({user_id: 10}) //message表(集合)里删除一条记录
WriteResult({ "nRemoved" : 1 })
> db.message.find()
{ "_id" : ObjectId("58a55ef57bc9a525061d0608"), "title" : "hello", "content" : "Hello world!" }
> db.message.update({title: 'hello'}, {$set: {title: 'Hello'}}) //message表(集合)里更新一条记录
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.message.find()
{ "_id" : ObjectId("58a55ef57bc9a525061d0608"), "title" : "Hello", "content" : "Hello world!" }
~~~
更多关于MongoDB的操作，可参见:

- [mongodb-tutorial](http://www.runoob.com/mongodb/mongodb-tutorial.html)
