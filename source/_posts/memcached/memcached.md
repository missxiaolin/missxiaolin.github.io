---
title: memcached安装
date: 2017-09-02 15:30:46
categories: ["PHP"]
tags: 'php'
---

### memcached安装

Memcached是一个分布式、高性能、内存缓存系统，主要是用来加快高负载数据库类型网站的访问速度。它也可以被用来存储任何类型的对象。几乎每一个流行的CMS有一个插件或模块利用memcached，和许多编程语言都有一个缓存库，包括PHP，Perl，Ruby，Python。缓存在内存中运行，因此是相当快的，因为它不需要将数据写入到磁盘。

## 下面以Centos 7.x服务器为例，演示如何安装。

### 安装

~~~
yum -y install memcached
~~~

## 配置

配置文件默认路径为 /etc/sysconfig/memcached

配置如下

~~~
PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"
OPTIONS=""
~~~

- PORT 运行的端口
- USER 运行的用户
- MAXCONN 最大连接数
- CACHESIZE 缓存大小，单位M

### 开启开机启动

~~~
systemctl enable memcached
~~~

### 启动

~~~
systemctl start memcached
~~~

### 查看状态

~~~
[root@localhost ~]# systemctl status memcached
● memcached.service - Memcached
   Loaded: loaded (/usr/lib/systemd/system/memcached.service; enabled; vendor preset: disabled)
   Active: active (running) since 六 2017-08-26 13:09:06 CST; 1min 32s ago
 Main PID: 17738 (memcached)
   CGroup: /system.slice/memcached.service
           └─17738 /usr/bin/memcached -u memcached -p 11211 -m 64 -c 1024
~~~

## Mac下面安装

~~~
# 列出所有memcached先关的包
brew search memcached
# 安装memcached以及memcached的PHP扩展
brew install php70-memcached
# 以后台方式启动memcached, 存储大小24MB，端口11211 (默认)
memcached -d -m 24 -p 11211
~~~

## 清空Memcached

方法一

~~~
telnet 192.168.1.xxx 11211
flush_all
~~~

方法二

~~~
echo "flush_all" | nc 192.168.1.xxx 11211
~~~