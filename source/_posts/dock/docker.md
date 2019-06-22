---
title: Docker 初级入门
date: 2018-06-22 10:16:46
categories: "技术杂谈"
tags: 'Docker'
---

Docker从2013年发布第一个版本以来，已经火遍全球，技术迭代也比较频繁，其周边产品和技术也越来越丰富。Docker的轻量级容器不仅实现了资源隔离，而且几乎可以运行在任何地方，使得部署和扩展变得非常容易，随着Docker的日趋完善，目前Docker已经被越来越多的公司应用到生产环境中。Docker从2013年发布第一个版本以来，已经火遍全球，技术迭代也比较频繁，其周边产品和技术也越来越丰富。Docker的轻量级容器不仅实现了资源隔离，而且几乎可以运行在任何地方，使得部署和扩展变得非常容易，随着Docker的日趋完善，目前Docker已经被越来越多的公司应用到生产环境中。

## 环境

1.1、宿主机操作系统环境

Centos7.1-64

1.2、docker版本

Server Version: 1.9.1

1.3、集群网络环境

master:10.2.0.80

slave1:10.2.0.77

slave2:10.2.0.134

服务发现：10.2.0.99

## 介绍一下各组件

1、dockerDocker daemon引擎

2、consul服务发现和配置共享的服务软件

3、swarm基于docker的集群调度管理软件，docker 1.2版本中已经自动集成集群功能了。

4、rethinkdbRethinkDB是一个完全支持Memcached协议、数据可持久化的工业级key-value存储系统，它自带了cluster和web资源管理功能。

5、shipyarddocker可视化资源管理平台

6、registrator服务自动注册

7、nginxweb服务代理软件

8、consultemplatedocker服务自动发现软件，这个要结合nginx使用，当我们在宿主机上启动一个容器服务时，这时候consultemplate就会自动从consul服务上发现在这个容器，并更新nginx配置文件。

9、cadvisorgoogle公司开源的docker容器资源监控软件

10、influxdbInfluxDB是一个开源分布式时序、事件和指标数据库。使用 Go 语言编写，无需外部依赖。其设计目标是实现分布式和水平伸缩扩展。

11、grafana图表展现服务，功能非常强大。12、graylog+ Elasticsearch具有报警选项的可插入日志和事件分析服务器
