---
title: MySQL查找和删除重复记录
date: 2018-09-14 7:52:46
categories: "mysql"
tags: ['mysql']
---

人在江湖飘，岂能不挨刀。写代码的总有考虑不周的时候，出现重复记录的问题，这时候就牵扯到要清洗重复的数据，那么如何查找到重复记录，并删除呢？

下面以MySQL数据库为例，进行演示。

## 前置准备

假设有一张成绩表，正常情况下每人一条成绩记录。但是不知道什么原因，有人出现多条成绩记录。

### 创建成绩表

~~~
CREATE TABLE `test` (
  `id` int(11) NOT NULL,
  `name` varchar(10) NOT NULL,
  `score` int(11) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '记录插入时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

### 插入测试数据

~~~
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES (1, '张三', 88, '2018-05-01 08:00:13');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES (2, '李四', 87, '2018-05-01 12:12:13');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES (3, '王五', 60, '2018-05-01 18:50:23');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES (4, '麻六', 89, '2018-05-01 20:12:56');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES	(5, '张三', 60, '2018-05-03 09:30:45');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES	(6, '李四', 79, '2018-05-05 15:01:43');
INSERT INTO `test` (`id`, `name`, `score`, `created_at`) VALUES	(7, '王五', 98, '2018-05-06 19:04:57');
~~~

### 预览原数据

~~~
mysql> select * from test;
+----+--------+-------+---------------------+
| id | name   | score | created_at          |
+----+--------+-------+---------------------+
|  1 | 张三   |    88 | 2018-05-01 08:00:13 |
|  2 | 李四   |    87 | 2018-05-01 12:12:13 |
|  3 | 王五   |    60 | 2018-05-01 18:50:23 |
|  4 | 麻六   |    89 | 2018-05-01 20:12:56 |
|  5 | 张三   |    60 | 2018-05-03 09:30:45 |
|  6 | 李四   |    79 | 2018-05-05 15:01:43 |
|  7 | 王五   |    98 | 2018-05-06 19:04:57 |
+----+--------+-------+---------------------+
7 rows in set (0.00 sec)
~~~

### 查询重复数据

~~~
mysql> select name,count(*) from test group by name;
+--------+----------+
| name   | count(*) |
+--------+----------+
| 张三   |        2 |
| 李四   |        2 |
| 王五   |        2 |
| 麻六   |        1 |
+--------+----------+
4 rows in set (0.00 sec)
~~~

### 查询出成绩记录个数大于1的学生

~~~
mysql> select name,count(*) from test group by name having count(*) > 1;
+--------+----------+
| name   | count(*) |
+--------+----------+
| 张三   |        2 |
| 李四   |        2 |
| 王五   |        2 |
+--------+----------+
3 rows in set (0.01 sec)
~~~

### 查询出含有重复记录的学生记录(重复的显示最高成绩)

~~~
mysql> select * from (select * from test order by score desc) as b group by name having count(name) > 1;
+----+--------+-------+---------------------+
| id | name   | score | created_at          |
+----+--------+-------+---------------------+
|  1 | 张三   |    88 | 2018-05-01 08:00:13 |
|  2 | 李四   |    87 | 2018-05-01 12:12:13 |
|  7 | 王五   |    98 | 2018-05-06 19:04:57 |
+----+--------+-------+---------------------+
3 rows in set (0.01 sec)
~~~

### 查询出含有重复记录的学生记录(重复的显示最低成绩)

~~~
mysql> select * from (select * from test order by score asc) as b group by name having count(name) > 1;
+----+--------+-------+---------------------+
| id | name   | score | created_at          |
+----+--------+-------+---------------------+
|  5 | 张三   |    60 | 2018-05-03 09:30:45 |
|  6 | 李四   |    79 | 2018-05-05 15:01:43 |
|  3 | 王五   |    60 | 2018-05-01 18:50:23 |
+----+--------+-------+---------------------+
3 rows in set (0.01 sec)
~~~

### 删除重复数据

#### 假设对于重复成绩的人，每个人只保留最低分数

~~~
mysql> delete from test where id in (select id from (select * from test order by score desc) as b group by name having count(name) > 1);
Query OK, 3 rows affected (0.03 sec)

mysql> select * from test;
+----+--------+-------+---------------------+
| id | name   | score | created_at          |
+----+--------+-------+---------------------+
|  3 | 王五   |    60 | 2018-05-01 18:50:23 |
|  4 | 麻六   |    89 | 2018-05-01 20:12:56 |
|  5 | 张三   |    60 | 2018-05-03 09:30:45 |
|  6 | 李四   |    79 | 2018-05-05 15:01:43 |
+----+--------+-------+---------------------+
4 rows in set (0.00 sec)
~~~

#### 假设对于重复成绩的人，每个人只保留最高分数

~~~
mysql> delete from test where id in (select id from (select * from test order by score asc) as b group by name having count(name) > 1);
Query OK, 3 rows affected (0.01 sec)

mysql> select * from test;
+----+--------+-------+---------------------+
| id | name   | score | created_at          |
+----+--------+-------+---------------------+
|  1 | 张三   |    88 | 2018-05-01 08:00:13 |
|  2 | 李四   |    87 | 2018-05-01 12:12:13 |
|  4 | 麻六   |    89 | 2018-05-01 20:12:56 |
|  7 | 王五   |    98 | 2018-05-06 19:04:57 |
+----+--------+-------+---------------------+
4 rows in set (0.00 sec)
~~~








