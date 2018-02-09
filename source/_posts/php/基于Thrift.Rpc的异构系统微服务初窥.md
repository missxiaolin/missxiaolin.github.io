---
title: 基于Thrift.Rpc的异构系统微服务初窥
date: 2018-02-09 16:30:46
categories: ["PHP"]
tags: 'php'
---

## 基于Thrift.Rpc的异构系统微服务初窥

- [php版本基础](http://www.cnblogs.com/qufo/p/5607653.html)
- [thrift之默认传输类TTransportDefaults和虚拟传输类TVirtualTransport](http://www.cnblogs.com/brucewoo/p/3226859.html)
- [thrift之TTransport层的堵塞的套接字I/O传输类TSocket](http://www.cnblogs.com/brucewoo/p/3226858.html)

### 微服务整体设计

<img src="http://oni42o7kl.bkt.clouddn.com/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%BC%82%E6%9E%84%E6%9E%B6%E6%9E%84.png">

### 单服务基本设计

<img src="http://oni42o7kl.bkt.clouddn.com/Thrift%E5%BE%AE%E6%9C%8D%E5%8A%A1%20-%20%E9%80%9A%E7%9F%A5%E6%9C%8D%E5%8A%A1.png">

### Thrift技术讲解

thrift是一个软件框架，用来进行可扩展且跨语言的服务的开发。它结合了功能强大的软件堆栈和[代码生成](https://baike.baidu.com/item/%E4%BB%A3%E7%A0%81%E7%94%9F%E6%88%90)引擎，以构建在 C++, Java, Go,Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and OCaml 这些编程语言间无缝结合的、高效的服务。

1.数据结构

- 基本类型（括号内为对应的Java类型）：
  bool（boolean）: 布尔类型(TRUE or FALSE)
  byte（byte）: 8位带符号整数
  i16（short）: 16位带符号整数
  i32（int）: 32位带符号整数
  i64（long）: 64位带符号整数
  double（double）: 64位浮点数
  string（String）: 采用UTF-8编码的字符串
- 特殊类型（括号内为对应的Java类型）
  binary（ByteBuffer）：未经过编码的字节流
- Structs（结构）：
  struct UserProfile {
    1: i32 uid,
    2: string name,
    3: string blurb
  }

  struct UserProfile {
    1: i32 uid = 1,
    2: string name = "User1",
    3: string blurb
  }
- 容器，除了上面提到的基本数据类型，Thrift还支持以下容器类型：
  list(java.util.ArrayList) set(java.util.HashSet) map（java.util.HashMap）
  struct Node {
    1: i32 id,
    2: string name,
    3: list<i32> subNodeList,
    4: map<i32,string> subNodeMap,
    5: set<i32> subNodeSet
  }
  struct SubNode {
    1: i32 uid,
    2: string name,
    3: i32 pid
  }
  struct Node {
    1: i32 uid,
    2: string name,
    3: list<subNode> subNodes
  }
- 服务
  service UserStorage {
    void store(1: UserProfile user),
    UserProfile retrieve(1: i32 uid)
  }

### 编译代码

- Go 使用 thrift -r --gen go:thrift_import=thrift App.thrift
- Php 使用 thrift -r --gen php:server,psr4 App.thrift

### 配合Nginx Tcp负载均衡搭建服务

~~~
upstream tcp_upstream_default {
    server  127.0.0.1:12001;
    server  127.0.0.1:12002;
}

server {
    listen              12000;
    proxy_connect_timeout   5s;
    proxy_timeout         30s;
    proxy_pass            tcp_upstream_default;
}
~~~



