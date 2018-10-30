---
title: swoole rpc
date: 2018-02-09 15:16:46
categories: ["PHP"]
tags: 'php'
---

### 介绍

RPC（Remote Procedure Call）——[远程过程调用](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8)，它是一种通过[网络](https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C)从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI[网络通信](https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C%E9%80%9A%E4%BF%A1)模型中，RPC跨越了[传输层](https://baike.baidu.com/item/%E4%BC%A0%E8%BE%93%E5%B1%82)和[应用层](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E5%B1%82/16412033)。RPC使得开发包括网络分布式多程序在内的应用程序更加容易。
RPC采用客户机/服务器模式。请求程序就是一个客户机，而服务提供程序就是一个服务器。首先，客户机调用进程发送一个有进程参数的调用信息到服务进程，然后等待应答信息。在服务器端，进程保持睡眠状态直到调用信息到达为止。当一个调用信息到达，服务器获得进程参数，计算结果，发送答复信息，然后等待下一个调用信息，最后，客户端调用进程接收答复信息，获得进程结果，然后调用执行继续进行。

有多种 RPC模式和执行。最初由 Sun 公司提出。IETF ONC 宪章重新修订了 Sun 版本，使得 ONC RPC 协议成为 IETF 标准协议。现在使用最普遍的模式和执行是开放式软件基础的分布式计算环境（DCE）。

### 应用

传输协议使用易用的JSON，调用RPC接口时，客户端会有执行前执行后两个事件，可以在执行前将Zipkin的信息封装到JSON中，而服务端代码执行前，执行后，可以利用额外的Zipkin数据很好的完成调用链记录。

实现依赖于php的call魔术方法。

<img src="http://www.missxiaolin.com/swoole-rpc.jpg">

### 项目地址

[swoole-rpc](https://github.com/missxiaolin/laravel-swoole-rpc-project)

