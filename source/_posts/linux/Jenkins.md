---
title: jenkins 安装使用
date: 2019-11-25 11:52:46
categories: "linux"
tags: [linux']
---

## Jenkins

Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

### 使用brew安装jenkins会避免很多其他安装方式产生的用户权限问题。

1.安装homebrew，在终端执行/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 命令安装homebrew；

2.使用homebrew安装jenkins，在终端执行 brew install jenkins。因为jenkins依赖java环境，所以这个命令未必会成功，如果缺少java环境，使用命令 brew cask info java 安装java环境，成功后再执行安装jenkins的命令；

3.安装jenkins成功后，使用 brew services start jenkins 启动Jenkins服务，使用 brew services stop jenkins停止Jenkins服务，使用 brew services restart jenkins重启Jenkins服务；

注：brew安装Jenkins会将httpListenAddress默认设置为127.0.0.1，样我们虽然可以在本地用localhost:8080访问，但是本机和局域网均无法用ip访问。解决办法为修改两个路径下的plist配置。并重启
～/Library/LaunchAgents/homebrew.mxcl.jenkins.plist
/usr/local/opt/jenkins/homebrew.mxcl.jenkins.plist
将上面两个plist中的httpListenAddress后的ip地址，修改为本机IP或者0.0.0.0即可。

需要的插件：GitLab，GitLab Hook，Xcode integration，Keychains and Provisioning Profiles Management，PostBuildScript，Git Parameter，Role-based Authorization Strategy(创建用户时分配权限)

### linux：

~~~
yum search openjdk
yum install java-1.8.0-openjdk
~~~

<img src="http://missxiaolin.com/Jenkins_20191122.png">

<img src="http://missxiaolin.com/Jenkins1_20191122.png">

安装Jenkins

~~~
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key //如果已经有 key 存在
sudo yum install jenkins
~~~

<img src="http://missxiaolin.com/Jenkins2_20191122.png">

启动Jenkins

~~~
sudo service jenkins start/stop/restart //显然，最后的参数分别对应启动、关闭、重启操作

sudo chkconfig jenkins on
~~~








