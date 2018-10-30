---
title: 非Java语言注入Eureka
date: 2018-04-02 16:30:46
categories: ["PHP"]
tags: 'php'
---

## 非Java语言注入Eureka

### 前言

本文档支持所有语言接入Eureka，但本作者是PHP开发工程师，所以，下面所有的样例以及思想都来源于PHP。

### PHP环境

既然要使用PHP来讲解如何注入Eureka，就不得不先介绍一下PHP的大环境。PHP作为一种脚本语言，活跃于广大Web开发环境中，其实已实属不易。随着时间的推进，PHP从简单的脚本语言，到现今的演变，成长也是有目共睹的，希望我们的PHP环境越来越棒吧。

众所周知，我们的PHP服务大多都跑在nginx、php-fpm下，每次接到一次请求，所有用到的php类库都会被重新执行，所有做到了非常方便的热部署。但显然，如果我们都了解一下Eureka的注入机制，你就会发现，我们这么跑起来的PHP服务，根本无法常驻内存。

### 使用Eureka的原因

我们为什么要使用Eureka呢？原因大体有三条：

- 他提供了完善的服务注册和服务发现。
- 他和Spring Cloud无缝集成
- 开源

### Eureka的工作原理

Eureka由三部分组成

- Eureka Server 提供服务注册与发现
- Service Provider 服务提供方，将自身服务注册到Eureka
- Service Consumer 服务消费方，从Eureka获取注册 服务列表，从而消费服务。

<img src="http://www.missxiaolin.com/eureka.jpg">

需要注意的是，上图三个角色都是逻辑角色，实际应用中，消费者和提供者可能是一个服务。

Eureka具体工作流程如下。

<img src="http://www.missxiaolin.com/eureka1.jpg">

Service Provider会向Eureka Server注册、续约、服务下线等操作。Eureka Server之间会做服务同步，保证多个Eureka Server之间，服务保持一致。Service Consumer会向Eureka Server获取服务列表，然后进行服务消费。

### Eureka注册的配置

InstanceId 实例ID，注册和续约时，需要告诉Eureka具体的服务实例进行注册和续约。

hostName 当前实例的主机名称

port 服务端口

ipAddr IP地址

Eureka主要的配置就这4个，简单来看，分别是实例、名字、ip和端口。因为我们只需要知道这四个配置就可以返回具体服务。

### Eureka注册流程

注册Eureka最重要的两个点，便是注册以及续约。

注册所用的xml如下

~~~
<instance>
    <instanceId>{{APP_URL}}</instanceId>
    <hostName>{{APP_URL}}</hostName>
    <port enabled="true">80</port>
    <securePort enabled="false">443</securePort>
    <app>{{APP_NAME}}</app>
    <ipAddr>{{APP_URL}}</ipAddr>
    <status>UP</status>
    <countryId>1</countryId>
    <dataCenterInfo class="com.netflix.appinfo.InstanceInfo$DefaultDataCenterInfo">
        <name>MyOwn</name>
    </dataCenterInfo>
    <homePageUrl>http://{{APP_URL}}/</homePageUrl>
    <statusPageUrl>http://{{APP_URL}}/status</statusPageUrl>
    <healthCheckUrl>http://{{APP_URL}}/health</healthCheckUrl>
    <vipAddress>{{APP_NAME}}</vipAddress>
    <secureVipAddress>{{APP_NAME}}</secureVipAddress>
    <isCoordinatingDiscoveryServer>false</isCoordinatingDiscoveryServer>
    <actionType>ADDED</actionType>
</instance>
~~~

其中APP_URL、APP_NAME根据具体情况填写。

然后调用接口 POST /eureka/v2/apps/appID 即可将服务注册到Eureka中。

接下来再说一下续约，PUT /eureka/v2/apps/appID/instanceID，instanceID便是上述预先填写的instanceId。

### PHP具体实现

因为PHP通常跑在nginx、php-fpm环境下，无法实现常驻内存。所以，我们需要用到扩展Swoole。让他跑一个进程，完善我们上述所说的功能。

具体流程如下图。

<img src="http://www.missxiaolin.com/PHP%E6%B3%A8%E5%85%A5Eureka%E6%B5%81%E7%A8%8B.png">

首先我们写一个基于Swoole的RPC服务，这里提供一个 RPC实现仓库 ，然后我们重写一下Server基类。

~~~
<?php
namespace App\Core\Support\Server;
 
use App\Core\Support\Client\EurekaClient;
use Xin\Swoole\Rpc\Server;
use swoole_server;
use swoole_process;
 
class RpcServer extends Server
{
    public function beforeServerStart(swoole_server $server)
    {
        // 注册服务到Eureka
        EurekaClient::getInstance()->register();
 
        // 用于续约
        $process = new swoole_process(function (swoole_process $worker) use ($server) {
            while (true) {
                sleep(30);
                EurekaClient::getInstance()->heartbeat();
                EurekaClient::getInstance()->cacheServices();
            }
        });
 
        $server->addProcess($process);
        parent::beforeServerStart($server);
    }
}
~~~

然后启动我们的服务，就可以在Eureka的页面中看到我们注册的服务了。

<img src="http://www.missxiaolin.com/1521712457260.jpg">

然后我们将Eureka的服务信息全部缓存到Redis中，截图如下

<img src="http://www.missxiaolin.com/1521715056369.jpg">

然后便可以调用微服务中的接口了。

~~~
$client = EurekaClient::getInstance();
$baseUri = $client->getBaseUriByServiceName('phalcon');
 
$res = $this->post($baseUri);
$json = json_decode($res->getBody()->getContents(), true);
dd($json);
~~~

<img src="http://www.missxiaolin.com/1521715121215.jpg">
















