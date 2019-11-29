---
title: filebeat 日志系统搭建
date: 2019-11-27 11:52:46
categories: "linux"
tags: [linux']
---

## 介绍

日常维护运行在集群上的服务时，依次登录到机器上查看日志文件显然是非常低效的。另一方面，这些日志文件经常是有着良好的格式以及固定的路径。如果能将指定的日志文件批量导出到一个数据库里，无论是查看还是检索都会方便很多。这里记录一下在构建日志管理系统时的一些工具和方法，方便以后取用。

本文将构建的系统如下面的框图所示，用于管理日志文件（例如.log文件），并可以通过可视化工具Kibana显示、查找。

### Elasticsearch

ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。

### Logstash

Logstash 是一款强大的数据处理工具，它可以实现数据传输，格式处理，格式化输出，还有强大的插件功能，常用于日志处理。ruby开发

### Kibana

Kibana是一个开源的分析与可视化平台，设计出来用于和Elasticsearch一起使用的。你可以用kibana搜索、查看、交互存放在Elasticsearch索引里的数据，使用各种不同的图表、表格、地图等kibana能够很轻易地展示高级数据分析与可视化。  HTML语言和JavaScript编写

### Filebeat

Filebeat 是基于原先 logstash-forwarder 的源码改造出来的，无需依赖 Java 环境就能运行，安装包10M不到。

### 初步架构图

<img src="http://missxiaolin.com/log_20191122.png">

### mac 安装

~~~
brew tap elastic/tap
brew install elastic/tap/elasticsearch-full
brew install elastic/tap/filebeat-full
brew install elastic/tap/kibana-full
~~~

### Filebeat

安装路径：/usr/local/var/homebrew/linked/filebeat-full/
配置路径：/usr/local/etc/filebeat
日志路径：/usr/local/var/log/filebeat

### Elasticsearch

安装路径：/usr/local/var/homebrew/linked/elasticsearch-full
配置路径：（ES_PATH_CONF）/usr/local/etc/elasticsearch
日志路径：（path.logs）/usr/local/var/log/elasticsearch

### Filebeat

Filebeat的功能是监控log文件，并将更新的日志信息输出。

打开Filebeat的配置文件，brew安装的路径是 /usr/local/etc/filebeat/filebeat.yml

在Filebeat.inputs项下面配置需要监控的日志路径：

~~~
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log
~~~

### 启动

~~~
brew services elasticsearch-full

brew services filebeat-full

brew services kibana-full
~~~

### 效果图

<img src="http://missxiaolin.com/log1_20191122.png">

### 架构衍生一

<img src="http://missxiaolin.com/log3_20191122.png">

这种架构下我们把 Logstash 实例与 Elasticsearch 实例直接相连。Logstash 实例直接通过 Input 插件读取数据源数据(比如 Java 日志， Nginx 日志等)，经过 Filter 插件进行过滤日志，最后通过 Output 插件将数据写入到 ElasticSearch 实例中。

这个阶段，日志的收集、过滤、输出等功能，主要由这三个核心组件组成 Input 、Filter、Output

Input：输入，输入数据可以是 File 、 Stdin（直接从控制台输入） 、TCP、Syslog 、Redis 、Collectd 等

Filter：过滤，将日志输出成我们想要的格式。Logstash 存在丰富的过滤插件：Grok 正则捕获、时间处理、JSON 编解码、数据修改 Mutate 。Grok 是 Logstash 中最重要的插件，强烈建议每个人都要使用 Grok Debugger 来调试自己的 Grok 表达式

grok { match => ["message", "(?m)[%{LOGLEVEL:level}] [%{TIMESTAMP_ISO8601:timestamp}] [%{DATA:logger}] [%{DATA:threadId}] [%{DATA:requestId}] %{GREEDYDATA:msgRawData}"] }

Output：输出，输出目标可以是 Stdout （直接从控制台输出）、Elasticsearch 、Redis 、TCP 、File 等

这是最简单的一种ELK架构方式，Logstash 实例直接与 Elasticsearch 实例连接。优点是搭建简单，易于上手。建议供初学者学习与参考，不能用于线上的环境。

问题;

logstash运行在java环境中，如果使用logstash搜集日志，会消耗应用节点的系统资源；考虑用filebeat代替。
Filebeat 所消耗的CPU只有 Logstash 的70%，但收集速度为 Logstash 的7倍。从我们的应用实践来看，Filebeat 确实用较低的成本和稳定的服务质量，解决了 Logstash 的资源消耗问题。
日志量大的时候，logstash到elasticsearch这里的传输，会有数据丢失；考虑有kafka

为何不用redis
数据丢失：Redis 队列多用于实时性较高的消息推送，并不保证可靠。Kafka保证可靠但有点延时。

数据堆积：Redis 队列容量取决于机器内存大小，如果超过设置的Max memory，数据就会抛弃。Kafka 的堆积能力取决于机器硬盘大小。


引入消息队列和filebeat

### 架构衍生二

<img src="http://missxiaolin.com/log4_20191122.png">

### 部署参考

ELK 整体
https://www.jianshu.com/p/f3658d267b5d

filebeat

https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html

http://www.cnblogs.com/bixiaoyu/p/9487539.html

kafka

https://www.cnblogs.com/5iTech/articles/6043224.html

https://www.cnblogs.com/luotianshuai/p/5206662.html

https://my.oschina.net/tongyufu/blog/1806196

kafka-manage

https://blog.csdn.net/isea533/article/details/73727485

https://www.cnblogs.com/dadonggg/p/8205302.html

https://blog.csdn.net/xhpscdx/article/details/76670209 (指标解释)

logstash

https://blog.csdn.net/gb4215287/article/details/73792105?utm_source=itdadao&utm_medium=referral

https://www.cnblogs.com/qingqing74647464/p/9378385.html

ES

https://www.cnblogs.com/aubin/p/8012840.html

https://www.cnblogs.com/leeSmall/p/9220535.html

https://www.cnblogs.com/leeSmall/p/9189078.html

Kibana

https://www.elastic.co/guide/cn/kibana/current/settings.html

elasticsearch-HQ

http://www.cnblogs.com/mliu/p/9413601.html

工具集

https://blog.csdn.net/asdasdasd123123123/article/details/81776758






