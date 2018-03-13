---
title: 基于Spring Cloud SideCar 构建异构PHP微服务
date: 2018-03-13 16:30:46
categories: ["PHP"]
tags: 'php'
---

## Sidecar 介绍

根据我的理解，Sidecar是作为一个代理的服务来间接性的让其他语言可以使用Eureka等相关组件。通过与Zuul的来进行路由的映射，从而可以做到服务的获取，然后可以使用Ribbon，Feign对服务进行消费，以及对Config Server的间接性调用。(此段内容仅个人理解，只作为参考，欢迎讨论，同时有误请及时指正。)

### 应用场景

如果我们需要在Spring Cloud中使用java的部分服务，除了通过zuul网关映射之外，我们还可以直接注入到Eureka中，通过各个服务的名字获取到当前服务的调用地址和端口。

~~~
[
    {
        "host": "limxMacBook-Pro.local",
        "port": 8887,
        "serviceId": "BOOTSTRAP",
        "uri": "http://limxMacBook-Pro.local:8887",
        "secure": false
    }
]
~~~

然后我们便可以根据uri和我们定义好的路由，进行接口访问。当然其他java服务也可以听过sidecar直接访问到php接口。接下来我们来说一下PHP代码注入到Eureka中的步骤。

### PHP注入Eureka

首先当时是配置sidecar了，我们来看一下基本配置

~~~
server:
  port: 5678
spring:
  application:
    name: php-service
 
sidecar:
  port: 8000
  health-uri: http://localhost:8000/health.json
~~~

sidecar的配置十分简单，基本除了端口就是一个健康检查的地址。服务端口是5678，所以eureka中注册的端口其实是5678，但是实际访问的接口，却是我们后面设置的8000端口。这里就有一个问题，因为eureka会获取当前主机的内网ip，所以如果实际的PHP代码如果和sidecar不在一台机子上，那一切都要完蛋。所以，这就要求我们的PHP部署，需要在Nginx层主动设置端口号！！！！80不是你想用，想用就能用！！！

接下来的过程就十分简单了，运行各部分服务。Erueka中就会显示我们的PHP服务，java服务也可以通过Zuul调用PHP服务。接下来需要说的就是PHP服务通过Eureka调用java服务。我们通过http://localhost:5678/hosts/$service就可以拿到其他服务的hosts配置，结果如应用场景中显示的一样，这样uri和接口我们就都知道了，然后再进行拼接，就可以正常访问接口了。（为啥不直接走Zuul呢？这样连sidecar都省了，可能是为了让java方便调用PHP吧）

### 总结

其实SpringCloud微服务基于http实现，所以一味的注入进Eureka并不是什么最好的方案。现在我们直接基于内部DNS解析，内部SLB负载均衡这种方式，也是一直方便的解决方案。但是。你以为这篇文档这样就结束了么？？Too Simple！！

就算我们不需要注入Eureka，但是SpringCloud得其他优势，我们还是想插一脚进来的。接下来简单说一下，PHP如何接入全链路监控。

### PHP接入全链路监控Zipkin

具体Demo就不放出来了，因为我做了一个基于Thrift的RPC链路监控DEMO，再做一个http的实在没必要，因为使用http接口接入Zipkin实在是太简单了！！！链接：PHP接入[Zipkin](https://missxiaolin.github.io/2018/02/09/php/PHP%E6%8E%A5%E5%85%A5Zipkin/)

这里大体说一下思路，首先我们知道，http请求，我们可以在header里装很多东西，request和response都可以。那我们把Zipkin的上下文直接扔到这里是十分简单的。然后在没个逻辑处理位置，比如有公用的request和response模块，我们就能很方便的把zipkin请求参数组装起来，然后扔到消息队列里，一切看起来是多么的简单清爽。（事实也是这样的）

### 基于Jenkins的持续集成方案

接下来再介绍的，便是PHP的持续集成方案。我研究了基于github的Travis持续集成，配合phpunit对代码进行单元测试。但是发现，这玩意只给github用。。没办法，只能另辟蹊径了，gitlab听说有个配套的gitlab ci，但是我发现了jenkins。于是我便开始docker 安装jenkins的经历。其中，问题当然不是出在jenkins上，而是在docker上！！！docker安装的jenkins，真的是非常清爽的jenkins，根本没有PHP环境，玩毛呢？？

接下来便是Dockerfile编写之旅。。

[搭建自带PHP环境的Jenkins Docker镜像](https://missxiaolin.github.io/2018/03/13/dock/DockerInstall/)






