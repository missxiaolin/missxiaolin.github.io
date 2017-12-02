---
title: kafka
date: 2017-13-03 12:16:46
categories: ["PHP","yii"]
tags: 'kafka'
---

## kafka 安装

mac，大数据套装之kafka安装及使用

首先，如果你现在还没有安装zookeeper，不用着急，在brew下安装kafka，是会自动安装zookeeper的。

~~~
brew install kafka
~~~

kafka的默认配置文件，也是不用修改的。您可以从下面的位置找到它。/usr/local/etc/kafka 。

### kafka的启动命令，如下。如果您还没有启动zookeeper，那么您可能需要的命令如下：

~~~
zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties & kafka-server-start /usr/local/etc/kafka/server.properties
~~~

### 启动kafka的命令就是

kafka-server-start /usr/local/etc/kafka/server.properties

### kafka的正常使用，需要先创建一个topic。注意，这里需要新开一个终端哦，不能在kafka的start的终端里面执行的。

~~~
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test1
~~~

### 当然，您还可以用下面的命令，查看所有已经创建好的topic。

~~~
kafka-topics --list --zookeeper localhost:2181
~~~

### 然后，就是producer命令了。

~~~
kafka-console-producer --broker-list localhost:9092 --topic test1
~~~

### 最后，就是consumer命令了。

~~~
kafka-console-consumer --bootstrap-server localhost:9092 --topic test1 --from-beginning
~~~

### 在这个mac版本中，使用老的consumer命令，已经不能正常收到数据了.... 话说，win10下面的类似命令还是可以收到数据的。

~~~
kafka-console-consumer --zookeeper localhost:2181 --topic test1--from-beginning
~~~