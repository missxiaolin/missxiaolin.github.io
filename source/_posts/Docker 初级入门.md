---
title: Docker 初级入门
date: 2017-08-26 10:16:46
categories: "技术杂谈"
tags: 'Docker'
---

## Docker

旨在指导成员快速对Docker 理解并使用到开发环境中。它能在多个维度加快开发效率，快速对市面上已知的软件产品理解与应用。
当然，对于产品级Docker级的应用及其编排策略才是Docker大显身手之地，但此篇文章不作太多赘述。

## 分步指南

1.What， Docker 是什么
2.Why,    为什么要用
3,理解其简单结构并可使用。

What is Docker
1，Docker是什么
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。
上述为百度“Docker”原话粘贴，可以自行再去搜索更多相关解释。这里标了两个关键字 “容器”， 和“可移植”。 由于它利用的是Linux内核的LXC（一个开源项目）技术，所以目前只能运行于Linux操作系统之下，所有你看到的有关 Windows及 Mac或非Linux(“非”包括Unix和BSD等)的Docker安装，都是基于一层VM即虚拟机之上搭建。
针对于容器， 对其理解需要记住：不同于虚拟机VM，通常基于物理机之上，操作系统级别，出现故障第一时间是修复；而容器是基于操作系统之上，进程级别， 出现故障第一时间是干掉，即kill。（PS：虽然你发现Docker在分层上是 scratch > [OS] > [Env套件] ，还是有一层OS，但你在宿主机上ps -A 会看到容器内的运行进程的。实现细节有兴趣可以自行了解）


| | 宿主 | 级别（虚拟出对象）| 出问题解决方案 | 对资源配比利用 |
| ------ || ------ | ------ | ------ | | ------ |
| VM | 物理机 | OS | 找到问题并修复 | |  |
| LXC | OS | 进程 | Kill掉 重启一个 | |  |

对于 可移植，是说容器是创建于定义好的镜像 的，镜像是什么？ 比较于 类和对象的理解，可以把镜像看成是类，而容器是某个类的对象， 即是镜像的一个实例。

通常通过Dockerfile创建(build)出的产物即是镜像(image)，用散列码（image id）区分，该产物（image）可以上传(push)于Docker Registry, 并可被任意安装了Docker的Linux下载(pull), 从而在该机上启动（run）一个容器实例（container）. 运行于 Linux机器的上的多个容器也用散列码（container）id区分。

可以这样理解：image叫做 容器类别 ， container是 容器实例。

下面用流程图来分步解释常用的命令：

<img src="http://oni42o7kl.bkt.clouddn.com/Docker.jpg">

从Create Image--→ Created Image; 1 从Pull Image ---→ Pulled Image; 2 从Run Docker Container ---→ Ran Docker Container ; 3 一共三个流程。
首先介绍两个概念， Registry, 和 Repository.
这两个库存的概念都是在它的移植性上的。
Registry是中央仓库，用以集中存放Docker 镜像的一个Service, 像阿里，Daocloud都有存放docker 镜像的中央仓库。你也可以搭建自己的Docker registry, 新版(3.x以上)的Nexus集成了这个功能。

官方的Registry是docker.io, 也是默认的Registry, 在需要填入registry的地方可省略，即默认使用官方中央仓库。 如 docker login [daocloud.io]Repository,是在中央仓库中，对镜像的分类。通常你可以在中央仓库中建立组织（Group）, 在组下建立 Repository, 类似github存放代码的管理形式, 所以你查看中央仓库中的GUI web页面，是通过 hub.docker.com访问的， 也是一种hub. docker Hub。 对于存放于每一个Repository的不同 image可以打上标签(tag)进行区分，通常用的是版本号。

例如，你的团队 monster开发的一个软件产品叫 playgroud, 那么你建立的该产品的docker 镜像就是 monster/playgroud。

你公司自建中央仓库地址为d.enmonster.com 就是d.enmonster.com/monster/playground. 该软件产品会进行版本迭代，那么就会有 [d.enmonster.com/monster/playground:1.1.1.](http://d.enmonster.com/monster/playground:1.1.1)
这样一个产品，对于上图流程1，就是 docker build -t d.enmonster.com/monster/playground:1.1.1 -f [/path/to/Dockerfile] 建立成功后 docker push d.enmonster.com/monster/playground:1.1.1

那么其它人，或你在其它机器上拿这个镜像的方法就是流程2， docker pull [d.enmonster.com/monster/playground:1.1.1](http://d.enmonster.com/monster/playground:1.1.1)

在docker images 可以查到有这个镜像 在你的机器上时，就可以通过docker run d.enmonster.com/monster/playground:1.1.1来运行起这样一个实例。
当然，通常docker run 需要很多其它参数。

正常运行后，docker ps 可以查看到正在运行的实例列表。可以通过 docker stop [container id]/[container name] 或 docker restart [container id]/[container name]对正在运行的容器进行 停止和 重启操作。docker ps -a 可以查看到所有本机的容器实例。可以通过docker start, docker rm 对该实例列表中已停止的实例进行启动和删除操作。

最好不要： 由于docker 是进程级别的虚拟技术，虽然用操作系统层来隔离与宿主的环境，但是最好不要在一个容器中挂起多个进程。你完全可以把docker 本身看作一种进程管理工具。一个容器就起一个进程，在容器中是默认 pid=1的那个进程。

## Why docker

2，为什么用Docker
Docker 的作用，从根本上是解决 软件产品交付时，对环境的依赖问题。
例如，你在CentOS7下开发出一款 基于Python3.6构建的软件产品。在交付使用方时，你需要书写说明书向使用方说明 该如何运行起你的软件产品。在你费了大量的人力物力向其解释，你需要在某台机器上安装Centos系统，再在CentOS中安装 了Python3.6，再安装pip, 通过pip 安装好依赖包列表中的所有依赖后，再运行你的产品。使用方会有各种各样的反馈，诸如“它为什么不能在Windows上运行。” “我机器上有python2并不想再装python3, 怕其互相影响”。 诸如此类的问题。会大大延长软件产品的交付时间。
这种情况不仅存在于公司间的乙方向甲方交付软件产品的情况，也存在于公司内部，开发向运维交付产品时。
另外一方面在成群大规模的集群 软件产品持续批量部署上，带来很大的时间成本与维护成本的节省 。 基于本文为初级入门，现只针对单机开发时，它所带来的好处作简单介绍：
基本上你喜欢在虚拟机里做的事情，容器都可以做到，而且你会发现这些事情，你会更喜欢用容器去做。
1, 沙箱，环境隔离，为了踩一些试验性的坑，你不想这个坑影响到你的OS环境，以及OS其它软件的环境。
2，多机集群试验，你可以启动起多个镜像的容器实例，把它们联合起来，做集群部署试验。实例间组网实验。
相比于虚拟机，它有更进一步的优势在于：
3，秒级启动，停止。
4，无需维护，有故障直接kill, 重新run 一份。
5，对于听说的概念或软件产品，更加快速的体验其使用效果。例如，在听说物联网中一个MQTT的协议，你发现了有eclipse-mosquitto这样一个产品，你可以在docker hub中搜到它，并用docker pull + docker run 马上在你本地的docker 中跑起一个。而不像传统方式，无论物理机还是虚拟机，需要先下载该软件产品，安装它的依赖，java等等，有可能需要tomcat依赖到的jar 包。再去命令跑起。
你会很快发现，基本上在速度这个维度上，为你的工作，带来很大的提升。
最好：还是容器关键，进程 级别，少用它作数据保存，维护工具。它更多做的是运算，将数据保存在宿主机上映射到容器中是最优的做法。

## 网络拓扑

对在你机器上建立的Docker 的网络拓扑作一个简单的图示展现，便于更好更快地进行操作理解。

<img src="http://oni42o7kl.bkt.clouddn.com/Network.png">

比如在一些调通配置上，如果理解成这样的具象，一些问题会不好排查到。

## 常用命令

最后，简单列下我在使用时，经常用到的命令。
对镜像的操作：
docker images //列出本机所有容器镜像
docker build //从Dockerfile描述中创建镜像
如 docker build -t enmonster/crm:1.0.1 -f Dockerfile . 句点指当前目录，f 参数是optional 的，默认会取所指目录下的Dockerfile
docker pull
如 docker pull python. 此命令有很多默认项，全部是： docker pull docker.io/library/python:latest
docker push
如 docker push enmonster/crm:latest
docker rmi
如 docker rmi enmonster/crm:latest
docker run
如docker run - -name python -it -d -v docker.io/library/python
对容器的操作:
docker ps -a // 列出本机所有容器实例 去掉a是列出运行中的容器实例。
docker start python //
docker stop python
docker restart python
docker exec python
docker attach python
docker rm python
可以使用Kitematic这样的GUI工具辅助你更快的查看运行于机器上的 docker容器, 像是进程管理器，或是开发环境下，像一个运行于本机的Services列表。
当然它还存在一些bug, 有时状态并不一定是同步的。
如下，展示一下Kitematic的界面，以及在同步的情况下，运行中的容器 和所有已经创建的容器列表的对应

<img src="http://oni42o7kl.bkt.clouddn.com/WechatIMG10.jpeg">
<img src="http://oni42o7kl.bkt.clouddn.com/WechatIMG9.jpeg">
