---
title: timestamp default 默认值问题
date: 2017-04-26 17:16:46
categories: "技术杂谈"
tags: 'linux'
---

mysql 中有个timestamp类型
今天使用了忽然发现执行发现
Mysql sql_mode设置 timestamp default 0000-00-00 00:00:00 创建表失败处理

### 解决方案

mysql5.7默认为
~~~
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
~~~

改为
~~~
sql_mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
~~~

这样之前插入语句就能正常执行了

### sql_mode 常用值说明

官方手册专门有一节介绍 [https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html) 。 SQL Mode 定义了两个方面：MySQL应支持的SQL语法，以及应该在数据上执行何种确认检查。

### 设置 sql_mode

~~~
查看当前连接会话的sql模式：
mysql> select @@session.sql_mode;
或者从环境变量里取
mysql> show variables like "sql_mode";
查看全局sql_mode设置：
mysql> select @@global.sql_mode;
只设置global，需要重新连接进来才会生效
~~~

设置
~~~
mysql> set sql_mode='';
mysql> set global sql_mode='NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES';
如果是自定义的模式组合，可以像下面这样
Adding only one mode to sql_mode without removing existing ones:
mysql> SET sql_mode=(SELECT CONCAT(@@sql_mode,','));
Removing only a specific mode from sql_mode without removing others:
mysql> SET sql_mode=(SELECT REPLACE(@@sql_mode,'',''));
~~~

忽然发现这样写电脑重启重启mysql就又要设置
那我们就去修改下配置文件吧（忽然发现mac没有my.cnf配置文件）
那就自己写一个把 放在/usr/local/etc/
~~~
[mysqld]
sql_mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
~~~



