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

### mysql 分表表

~~~
CREATE TABLE `ue_user` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '自动编号',
  `mobile` varchar(11) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '手机号',
  `halt` varchar(8) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '秘钥',
  `created_at` datetime NOT NULL COMMENT '创建时间',
  `updated_at` datetime NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `halt` (`halt`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
PARTITION BY RANGE (id)
(PARTITION p0 VALUES LESS THAN (5000000) ENGINE = InnoDB,
 PARTITION p1 VALUES LESS THAN (10000000) ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN (15000000) ENGINE = InnoDB,
 PARTITION p3 VALUES LESS THAN (20000000) ENGINE = InnoDB,
 PARTITION p4 VALUES LESS THAN (25000000) ENGINE = InnoDB,
 PARTITION p5 VALUES LESS THAN (30000000) ENGINE = InnoDB,
 PARTITION p6 VALUES LESS THAN (35000000) ENGINE = InnoDB,
 PARTITION p7 VALUES LESS THAN (40000000) ENGINE = InnoDB,
 PARTITION p8 VALUES LESS THAN (45000000) ENGINE = InnoDB,
 PARTITION p9 VALUES LESS THAN (50000000) ENGINE = InnoDB,
 PARTITION p10 VALUES LESS THAN (55000000) ENGINE = InnoDB,
 PARTITION p11 VALUES LESS THAN (60000000) ENGINE = InnoDB,
 PARTITION p12 VALUES LESS THAN (65000000) ENGINE = InnoDB,
 PARTITION p13 VALUES LESS THAN (70000000) ENGINE = InnoDB,
 PARTITION p14 VALUES LESS THAN (75000000) ENGINE = InnoDB,
 PARTITION p15 VALUES LESS THAN (80000000) ENGINE = InnoDB,
 PARTITION p16 VALUES LESS THAN (85000000) ENGINE = InnoDB,
 PARTITION p17 VALUES LESS THAN (90000000) ENGINE = InnoDB,
 PARTITION p18 VALUES LESS THAN (95000000) ENGINE = InnoDB,
 PARTITION p19 VALUES LESS THAN MAXVALUE ENGINE = InnoDB)
;
~~~



