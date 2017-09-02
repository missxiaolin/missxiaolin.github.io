---
title: Workerman
date: 2017-04-22 11:16:46
categories: "技术杂谈"
tags: 'php'
---

### Workerman - 高性能的PHP socket框架

[WorkerMan](http://doc3.workerman.net)是一款纯PHP开发的开源的高性能的PHP socket服务器框架，基于WorkerMan开发者可以开发出各种网络服务器，即时IM通讯，游戏服务器通讯，与硬件传感器通讯等等，开发这些应用程序我们无法直接使用nginx/apache + PHP来实现，几乎任何基于TCP/UDP通讯的服务端都可以用WorkerMan来开发。WorkerMan使得开发者摆脱PHP只能用于Web开发的束缚，向更广阔的前景发展。

## 特性

- 纯 PHP开发
- 支持PHP多进程
- 支持TCP、UDP
- 支持长连接
- 支持各种应用层协议
- 支持高并发
- 支持服务平滑重启
- 支持文件更新检测及自动加载
- 支持以指定用户运行子进程
- 支持对象或者资源永久保持
- 高性能
- 支持HHVM
- 支持分布式部署
- 支持守护进程化
- 支持多端口监听
- 支持标准输入输出重定向

### 安装配置

1.[pcntl扩展](http://cn2.php.net/manual/zh/book.pcntl.php)
pcntl 扩展是 PHP 在 Linux 环境下进程控制的扩展

2.[posix扩展](http://cn2.php.net/manual/zh/book.posix.php)
posix扩展使得PHP在Linux环境可以调用系统通过[POSIX](http://baike.baidu.com/view/209573.htm)标准提供的接口

3.[libevent扩展](http://cn2.php.net/manual/en/book.libevent.php) 或者 [Event扩展](http://php.net/manual/zh/book.event.php)
libevent扩展(或者event扩展)使得PHP可以使用系统[Epoll](http://baike.baidu.com/item/epoll)、Kqueue等高级事件处理机制，能够显著提高WorkerMan在高并发连接时CPU利用率。在高并发长连接相关应用中非常重要。（非必要安装，默认使用PHP 原生 Select 事件处理机制）

~~~
Workerman                      // workerman内核代码
    ├── Connection                 // socket连接相关
    │   ├── ConnectionInterface.php// socket连接接口
    │   ├── TcpConnection.php      // Tcp连接类
    │   ├── AsyncTcpConnection.php // 异步Tcp连接类
    │   └── UdpConnection.php      // Udp连接类
    ├── Events                     // 网络事件库
    │   ├── EventInterface.php     // 网络事件库接口
    │   ├── Libevent.php           // Libevent网络事件库
    │   ├── Ev.php                 // Libev网络事件库
    │   └── Select.php             // Select网络事件库
    ├── Lib                        // 常用的类库
    │   ├── Constants.php          // 常量定义
    │   └── Timer.php              // 定时器
    ├── Protocols                  // 协议相关
    │   ├── ProtocolInterface.php  // 协议接口类
    │   ├── Http                   // http协议相关
    │   │   └── mime.types         // mime类型
    │   ├── Http.php               // http协议实现
    │   ├── Text.php               // Text协议实现
    │   ├── Frame.php              // Frame协议实现
    │   └── Websocket.php          // websocket协议的实现
    ├── Worker.php                 // Worker
    ├── WebServer.php              // WebServer
    └── Autoloader.php             // 自动加载类
~~~

### 可用协议
~~~
// http协议
$worker1 = new Worker('http://0.0.0.0:1221');
// websocket协议
$worker2 = new Worker('websocket://0.0.0.0:1222');
// text文本协议（telnet协议）
$worker3 = new Worker('text://0.0.0.0:1223');
// frame文本协议（可用于二进制数传输）
$worker3 = new Worker('frame://0.0.0.0:1223');
// 直接基于tcp传输
$worker4 = new Worker('tcp://0.0.0.0:1224');
// 直接基于udp传输
$worker5 = new Worker('udp://0.0.0.0:1225');
// 还可以自己定制协议
// 使用telnet测试
~~~
