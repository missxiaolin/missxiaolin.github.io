---
title: laravel和elasticsearch
date: 2017-08-28 10:16:46
categories: "技术杂谈"
tags: 'elasticsearch'
---

## elasticsearch安装

elasticsearch中文发行版，针对中文集成了相关插件，方便新手学习测试.

[https://github.com/medcl/elasticsearch-rtf](https://github.com/medcl/elasticsearch-rtf)

## 安装laravel使用elastic的包

- 安装laravel/scout [http://d.larvel-china.org/docs/5.4/sout](http://d.larvel-china.org/docs/5.4/sout)
- 安装scout的es驱动[http://github.com/ErickTamayo/laravel-scout-elastic](http://github.com/ErickTamayo/laravel-scout-elastic)
- 修改scout.php

开始安装：

1.composer require laravel/scout

### mac

安装

~~~
brew install kibana
brew install elasticsearch
~~~

启动

~~~
brew services start kibana
brew services start elasticsearch
~~~

### ELK： elasticsearch，logstash，kibana

https://www.elastic.co/cn/products/logstash

https://www.elastic.co/cn/products/elasticsearch

https://www.elastic.co/cn/products/kibana

### Elasticsearch

ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。

### Logstash

 Logstash 是一款强大的数据处理工具，它可以实现数据传输，格式处理，格式化输出，还有强大的插件功能，常用于日志处理。ruby开发

### Kibana

Kibana是一个开源的分析与可视化平台，设计出来用于和Elasticsearch一起使用的。你可以用kibana搜索、查看、交互存放在Elasticsearch索引里的数据，使用各种不同的图表、表格、地图等kibana能够很轻易地展示高级数据分析与可视化。  HTML语言和JavaScript编写

### Filebeat

Filebeat 是基于原先 logstash-forwarder 的源码改造出来的，无需依赖 Java 环境就能运行，安装包10M不到。

ELK简单版架构如下：

<img src="http://oni42o7kl.bkt.clouddn.com/image2018-7-10_14-24-9.png">

这种架构下我们把 Logstash 实例与 Elasticsearch 实例直接相连。Logstash 实例直接通过 Input 插件读取数据源数据(比如 Java 日志， Nginx 日志等)，经过 Filter 插件进行过滤日志，最后通过 Output 插件将数据写入到 ElasticSearch 实例中。

这个阶段，日志的收集、过滤、输出等功能，主要由这三个核心组件组成 Input 、Filter、Output

Input：输入，输入数据可以是 File 、 Stdin（直接从控制台输入） 、TCP、Syslog 、Redis 、Collectd 等

Filter：过滤，将日志输出成我们想要的格式。Logstash 存在丰富的过滤插件：Grok 正则捕获、时间处理、JSON 编解码、数据修改 Mutate 。Grok 是 Logstash 中最重要的插件，强烈建议每个人都要使用 Grok Debugger 来调试自己的 Grok 表达式

grok { match => ["message", "(?m)[%{LOGLEVEL:level}] [%{TIMESTAMP_ISO8601:timestamp}] [%{DATA:logger}] [%{DATA:threadId}] [%{DATA:requestId}] %{GREEDYDATA:msgRawData}"] }

Output：输出，输出目标可以是 Stdout （直接从控制台输出）、Elasticsearch 、Redis 、TCP 、File 等

这是最简单的一种ELK架构方式，Logstash 实例直接与 Elasticsearch 实例连接。优点是搭建简单，易于上手。建议供初学者学习与参考，不能用于线上的环境。


问题;

1. logstash运行在java环境中，如果使用logstash搜集日志，会消耗应用节点的系统资源；考虑用filebeat代替。
2. Filebeat 所消耗的CPU只有 Logstash 的70%，但收集速度为 Logstash 的7倍。从我们的应用实践来看，Filebeat 确实用较低的成本和稳定的服务质量，解决了 Logstash的资源消耗问题。
3. 日志量大的时候，logstash到elasticsearch这里的传输，会有数据丢失；考虑有kafka

### 为何不用redis

数据丢失：Redis 队列多用于实时性较高的消息推送，并不保证可靠。Kafka保证可靠但有点延时。

数据堆积：Redis 队列容量取决于机器内存大小，如果超过设置的Max memory，数据就会抛弃。Kafka 的堆积能力取决于机器硬盘大小。

引入消息队列和filebeat

<img src="http://oni42o7kl.bkt.clouddn.com/image2018-7-10_14-52-7.png">







