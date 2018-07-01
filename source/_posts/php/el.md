---
title: el
date: 2018-06-30 16:30:46
categories: ["PHP"]
tags: 'php'
---

### elasticsearch

ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。

我们建立一个网站或应用程序，并要添加搜索功能，但是想要完成搜索工作的创建是非常困难的。我们希望搜索解决方案要运行速度快，我们希望能有一个零配置和一个完全免费的搜索模式，我们希望能够简单地使用JSON通过HTTP来索引数据，我们希望我们的搜索服务器始终可用，我们希望能够从一台开始并扩展到数百台，我们要实时搜索，我们要简单的多租户，我们希望建立一个云的解决方案。因此我们利用Elasticsearch来解决所有这些问题及可能出现的更多其它问题。

### 搜索

- /search: 所有索引，所有type下的所有数据都搜索出来
- /index/_search： 指定一个index，搜索其下所有type数据
- /index1,index2/_search: 同时搜索2个index下数据
- /*1,*2/_search: 按照通配符去匹配多个索引
- /index1/type1/_search: 搜索一个index下指定的type数据
- /index1,index2/type1,type2/_search: 搜索多个index下的多个type数据
- /all/type1,type2/_search: _all，可以代表搜索所有index下指定type数据

### 分页

- /_search?from=0&size=3: size(条数)，from(第几条开始)