---
title: php-fpm内存
date: 2019-04-23 9:52:46
categories: "linux"
tags: [linux']
---

## 前景

最近发现博客的内存老是隔三差五地被“吃掉”了。

LNMP架构中PHP是运行在FastCGI模式下，按照官方的说法，php-cgi会在每个请求结束的时候会回收脚本使用的全部内存，但是并不会释放给操作系统，而是继续持有以应对下一次PHP请求。而php-fpm是FastCGI进程管理器，用于控制php的内存和进程等。

所以，解决的办法就是通过php-fpm优化总的进程数和单个进程占用的内存，从而解决php-fpm进程占用内存大和不释放内存的问题。

### 分析判断php-fpm内存占用情况

如果你发现VPS主机出现了卡顿的情况，首先查看一下内存的占用情况，常用的命令就是Top、Glances、Free等，不了解这些命令的朋友可以先看看挖站否做的专题：[Linux系统监控命令整理汇总-掌握CPU,内存,磁盘IO等找出性能瓶颈](https://wzfou.com/linux-jiankong/)。

使用Glances命令，再按下m，就可以查看到当前VPS主机进程内存占用情况了，按照占用内存由多到少排序（或者使用Top命令，按下M，效果是一样的）。

查看当前php-fpm总进程数，命令：

~~~
ps -ylC php-fpm --sort:rss
ps -fe |grep "php-fpm"|grep "pool"|wc -l
~~~

其中RSS就是占用的内存情况。如下图：

<img src="http://missxiaolin.com/1556004847105_20190423.jpg">

查看当前php-fpm进程的内存占用情况及启动时间，命令如下：

~~~
ps -e -o 'pid,comm,args,pcpu,rsz,vsz,stime,user,uid'|grep www|sort -nrk5
~~~

查看当前php-fpm进程平均占用内存情况，一般来说一个php-fpm进程占用的内存为30-40MB，本次查询的结果是60MB，显然是多了。命令如下：

~~~
ps --no-headers -o "rss,cmd" -C php-fpm | awk '{ sum+=$1 } END { printf ("%d%s\n", sum/NR/1024,"M") }'
~~~

### 熟悉php-fpm配置文件说明

php-fpm.conf几个重要的参数说明如下：

~~~
pm = dynamic #指定进程管理方式，有3种可供选择：static、dynamic和ondemand。
pm.max_children = 16 #static模式下创建的子进程数或dynamic模式下同一时刻允许最大的php-fpm子进程数量。
pm.start_servers = 10 #动态方式下的起始php-fpm进程数量。
pm.min_spare_servers = 8 #动态方式下服务器空闲时最小php-fpm进程数量。
pm.max_spare_servers = 16 #动态方式下服务器空闲时最大php-fpm进程数量。
pm.max_requests = 2000 #php-fpm子进程能处理的最大请求数。
pm.process_idle_timeout = 10s
request_terminate_timeout = 120
~~~

pm三种进程管理模式说明如下：

~~~
pm = static，始终保持一个固定数量的子进程，这个数由pm.max_children定义，这种方式很不灵活，也通常不是默认的。

pm = dynamic，启动时会产生固定数量的子进程（由pm.start_servers控制）可以理解成最小子进程数，而最大子进程数则由pm.max_children去控制，子进程数会在最大和最小数范围中变化。闲置的子进程数还可以由另2个配置控制，分别是pm.min_spare_servers和pm.max_spare_servers。如果闲置的子进程超出了pm.max_spare_servers，则会被杀掉。小于pm.min_spare_servers则会启动进程（注意，pm.max_spare_servers应小于pm.max_children）。

pm = ondemand，这种模式和pm = dynamic相反，把内存放在第一位，每个闲置进程在持续闲置了pm.process_idle_timeout秒后就会被杀掉，如果服务器长时间没有请求，就只会有一个php-fpm主进程。弊端是遇到高峰期或者如果pm.process_idle_timeout的值太短的话，容易出现504 Gateway Time-out错误，因此pm = dynamic和pm = ondemand谁更适合视实际情况而定。
~~~

### 解决php-fpm进程占用内存大问题

1. 调整管理模式

static管理模式适合比较大内存的服务器，而dynamic则适合小内存的服务器，你可以设置一个pm.min_spare_servers和pm.max_spare_servers合理范围，这样进程数会不断变动。ondemand模式则更加适合微小内存，例如512MB或者256MB内存，以及对可用性要求不高的环境。

2. 减少php-fpm进程数

如果你的VPS主机的内存被占用耗尽，可以检查一下你的php-fpm进程数，按照php-fpm进程数=内存/2/30来计算，1GB内存适合的php-fpm进程数为10-20之间，具体还得根据你的PHP加载的附加组件有关系。

3. php-fpm配置示例

这里以1GB内存的VPS配置php-fpm为演示，实际操作来看设置数值还得根据服务器本身的性能、PHP等综合考虑。

~~~
pm = dynamic #dynamic和ondemand适合小内存。
pm.max_children = 15 #static模式下生效，dynamic不生效。
pm.start_servers = 8 #dynamic模式下开机的进程数量。
pm.min_spare_servers = 6 #dynamic模式下最小php-fpm进程数量。
pm.max_spare_servers = 15 #dynamic模式下最大php-fpm进程数量。
~~~

### 解决php-fpm进程不释放内存问题

上面通过减少php-fpm进程总数来达到减少php-fpm内存占用的问题，实际使用过程中发现php-fpm进程还存长期占用内存而不释放的问题。解决的方法就是减少pm.max_requests数。

最大请求数max_requests，即当一个 PHP-CGI 进程处理的请求数累积到 max_requests 个后，自动重启该进程，这样达到了释放内存的目的了。以1GB内存的VPS主机设置为例（如果你设置的数值没有达到释放内存可以继续调低）：

~~~
pm.max_requests = 500 
~~~

当php-fpm进程达到了pm.max_requests设定的数值后，就会重启该进程，从而释放内存。下图是我测试后的效果，可以看出php-fpm进程被强制结束并释放了内存。

### 总结

对于大内存以及对并发和可用性要求的话，建议使用static管理模式+最大的pm.max_children。如果是小内存的服务器，建议使用dynamic或者ondemand模式，同时降低pm.start_servers和pm.max_spare_servers进程数。

为什么我调整了参数没有达到应有的效果？根据wzfou.com的经验，php-fpm配置文件参数不能一概而论，必须要结合服务器自身的性能、WEB动态内容以及对可用性的要求来进行调整，内存长期占用最好是再检查一下是否有内存泄露。