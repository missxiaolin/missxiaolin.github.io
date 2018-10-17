---
title: redis安装
date: 2017-09-02 15:30:46
categories: ["PHP"]
tags: 'php'
---

## redis

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。跟Memcached相比，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set –有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。更多关于Redis和Memcache的比较，可参见[memcached redis 对比分析](http://www.jianshu.com/p/e94fa7340923)。

### 安装redis

MAC 系统

~~~
brew install redis
~~~

配置文件路径: /usr/local/etc/redis.conf

Centos Linux

~~~
yum install redis
~~~

配置文件路径: /etc/redis.conf

启动：

~~~
redis-server
~~~

之前我们还有说过docker这次我们正好来说下怎么使用

docker还不了解的可以看看[docker入门](https://missxiaolin.github.io/2017/08/26/dock/Docker%20%E5%88%9D%E7%BA%A7%E5%85%A5%E9%97%A8/)

直接搜索出来安装官方的就可以了->启动->设置->暴露出端口号

<img src="http://oni42o7kl.bkt.clouddn.com/docker-redis.png">
<img src="http://oni42o7kl.bkt.clouddn.com/redis-start-up.png">
<img src="http://oni42o7kl.bkt.clouddn.com/redis-set-up.png">

没错就是这么简单 redis就启动了

下面我们进入命令行

~~~
redis-cli  -p 6379
~~~

<img src="http://oni42o7kl.bkt.clouddn.com/redis-mlh.png">

#### dicket 命令行安装

~~~
# 拉取 redis 镜像
> docker pull redis
# 运行 redis 容器
> docker run --name myredis -d -p6379:6379 redis
# 执行容器中的 redis-cli，可以直接使用命令行操作 redis
> docker exec -it myredis redis-cli...
~~~

下面我们继续

## 检测redis是否启动成功

~~~
Victors-MBP:~ wangsheng$ redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> INFO
# Server
redis_version:4.0.1
......
127.0.0.1:6379> set message "Hello World"
OK
127.0.0.1:6379> get message
"Hello World"
127.0.0.1:6379> exit
~~~

是不是也很方便

## 设置Redis远程访问以及权限控制

(1). 编辑配置文件，添加如下内容

~~~
bind 192.168.1.xxx
requirepass <password>
~~~

(2). 重启redis

~~~
redis-server /path/to/redis.conf
~~~

## 客户端认证和连接

~~~
redis-cli -h 192.168.1.xxx -a <password>
192.168.1.xxx:6379> ping
PONG
~~~

或者

~~~
redis-cli -h 192.168.1.xxx
192.168.1.xxx:6379> ping
(error) NOAUTH Authentication required.
192.168.1.xxx:6379> auth <password>
OK
192.168.1.xxx:6379> ping
PONG
~~~

### 我们可以通过以下命令查看是否设置了密码验证

~~~
127.0.0.1:6379> CONFIG get requirepass
1) "requirepass"
2) ""
~~~

### 设置密码验证

~~~
CONFIG set requirepass "xiaolin"
~~~

设置密码后，客户端连接 redis 服务就需要密码验证，否则无法执行命令。

~~~
127.0.0.1:6379> AUTH xiaolin
~~~

## 安装Redis的PHP扩展

### MAC 系统

~~~
brew install php70-redis
~~~

配置文件路径: /usr/local/etc/php/7.0/conf.d/ext-redis.ini

### Centos Linux

~~~
yum install php70w-pecl-redis
~~~

配置文件路径: /etc/php.d/redis.ini

## Yii中配置Redis

(1). composer.json中添加yii2-reds中间件

~~~
"yiisoft/yii2-redis": "~2.0.0"
~~~

(2). 通过执行composer update安装

(3). 添加redis具体参数配置common/config/main.php

~~~
return [
    //....
    'components' => [
        'redis' => [
            'class' => 'yii\redis\Connection',
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 0,
        ],
    ]
];
~~~

## Yii中使用Redis

~~~
// 设置字符串类型的key-value
$result_str = Yii::$app->redis->set('str', 'Hello Redis!');
dump($result_str); // true
// 获取str对应的value
dump(Yii::$app->redis->get('str')); // Hello Redis!
// 查看所有的key
dump(Yii::$app->redis->keys('*')); // [0 => "str"]
// 删除key
$result_del = Yii::$app->redis->del('str');
dump($result_del); // 1
// 列表
$result_list = Yii::$app->redis->lpush('list', 'list1'); //list里数组的长度:1
dump($result_list);
$result_range = Yii::$app->redis->lrange('list', 0, 2); // 取出下标为[0, 2]
dump($result_range);
$result_update = Yii::$app->redis->lset('list', 1,'list2');
dump($result_update); // true
// 哈希 适合存储对象
$result_set = Yii::$app->redis->hmset('person', 'name', 'victor', 'age', 30);
dump($result_set); //true
dump(Yii::$app->redis->hgetall('person')); // [0=>"name", 1=>"victor", 2=>"age", 3=>"30"]
// 集合
$result_set = Yii::$app->redis->sadd('set', 'a', 'b', 'c', 'b');
dump($result_set); // 3
dump(Yii::$app->redis->scard('set')); //3
dump(Yii::$app->redis->smembers('set')); //[0=>"b", 1=>"c", 2=>"a"]
// 有序集合
$result_zset = Yii::$app->redis->zadd('zset', 1, 'a', 2, 'b', 3, 'c');
dump($result_zset); //3
dump(Yii::$app->redis->zcard('zset')); ///3
dump(Yii::$app->redis->zrange('zset', 0, 1)); //[0=>"a", 1=>"b"]
~~~


