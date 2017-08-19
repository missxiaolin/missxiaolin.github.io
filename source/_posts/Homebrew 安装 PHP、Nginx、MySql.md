---
title: Homebrew 安装 PHP、Nginx、MySql
date: 2017-04-23 15:16:46
categories: "技术杂谈"
tags: ['php','linux']
---

### 安装 Mysql

1.查找mysql

~~~
brew search mysql
automysqlbackup                      mysql-connector-c
homebrew/php/php53-mysqlnd_ms        mysql-connector-c++
homebrew/php/php54-mysqlnd_ms        mysql-sandbox
homebrew/php/php55-mysqlnd_ms        mysql-search-replace
homebrew/php/php56-mysqlnd_ms        mysql-utilities
mysql                                mysql@5.5
mysql++                              mysql@5.6
mysql-cluster                        mysqltuner
Caskroom/cask/mysql-connector-python
Caskroom/cask/mysql-shell
Caskroom/cask/mysql-utilities
Caskroom/cask/mysqlworkbench
Caskroom/cask/navicat-for-mysql
Caskroom/cask/sqlpro-for-mysql
~~~

2.查看版本信息
brew info mysql

3.执行安装启动

~~~
brew install mysql
mysql.server start
# 略...
# 安装完成后, 倒数第三行:
# We've installed your MySQL database without a root password. To secure it run:
#    mysql_secure_installation
# To connect run:
#    mysql -uroot
~~~

上面安装成功后提示的意思是说, 你安装的 MySql 没有密码, 执行mysql_secure_installation 进行设置密码, 其中会提示很多信息让你设置, 根据提示自己设置就行了。

4.安装完成进入mysql

~~~
mysql -uroot -p
~~~	

<img src="http://oni42o7kl.bkt.clouddn.com/%E5%AE%89%E8%A3%85mysql.png">

### 安装PHP

1.超找php信息
brew search php

2.执行安装
brew install php

3.安装成功
php -i | grep php.ini 可以查找php.ini

### 安装nginx

方法同上brew install nginx

配置文件在
/usr/local/Cellar/nginx/




