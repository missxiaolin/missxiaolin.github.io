---
title: Swoole安装
date: 2017-04-24 16:16:46
categories: "技术杂谈"
tags: ['php','linux']
---

[Swoole](http://www.swoole.com/)是PHP的异步、并行、高性能网络通信引擎，使用纯C语言编写，提供了PHP语言的异步多线程服务器，异步TCP/UDP网络客户端，异步MySQL，异步Redis，数据库连接池，AsyncTask，消息队列，毫秒定时器，异步文件读写，异步DNS查询。 Swoole内置了Http/WebSocket服务器端/客户端、Http2.0服务器端。

除了异步IO的支持之外，Swoole为PHP多进程的模式设计了多个并发数据结构和IPC通信机制，可以大大简化多进程并发编程的工作。其中包括了并发原子计数器，并发HashTable，Channel，Lock，进程间通信IPC等丰富的功能特性。

Swoole2.0支持了类似Go语言的协程，可以使用完全同步的代码实现异步程序。PHP代码无需额外增加任何关键词，底层自动进行协程调度，实现异步

Swoole可以广泛应用于互联网、移动通信、企业软件、云计算、网络游戏、物联网（IOT）、车联网、智能家居等领域。 使用PHP+Swoole作为网络通信框架，可以使企业IT研发团队的效率大大提升，更加专注于开发创新产品。

## 安装

访问 [Swoole Downloads](https://github.com/swoole/swoole-src/releases)下载源码，解压。

~~~
cd /path/to/swoole_source_code
phpize
./configure
make
sudo make install
~~~

swoole的./configure有很多额外参数,可以通过./configure –help命令查看,这里均选择默认项

安装成功后，需要修改php.ini文件，在php.ini文件中添加swoole配置，配置如下：

~~~
extension=swoole.so
~~~

随后在终端中输入命令 php -m 查看扩展安装情况。如果在列出的扩展中看到了swoole, 则说明安装成功。