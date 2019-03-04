---
title: mac电脑任何命令都是command not found
date: 2019-03-03 18:16:46
categories: "技术杂谈"
tags: ['mac']
---

## 前景

早上一来上班，第一件时间是打开IDEA，然后在命令窗口 git pull，一天的事故由此发生了， -bash: git: command not found！！！what？？？在尝试一下clear，还是不行，not found！！重启IDEA，重启电脑都没有效果。也是是IDEA有问题了，想要不重装一下IDEA，但是IDEA重装之后要安装插件，配置环境等等，我是一个比较懒还很怕麻烦的妹子，所以在试一下电脑的终端执行命令，看看是IDEA的问题，还是电脑的问题。 
终端的ls都找不到，还能不能愉快的玩耍了，我的天天呀！！！一周最后一天的工作日，难道它是想给我放个假/(ㄒoㄒ)/~~。 
绝望中突然想起来，昨天安装了一下go，接着配置了一下环境变量。。。。问题似乎是这里 
原来是我在配置环境变量的时候把系统自带的环境变量给丢了 


### 执行命令

~~~
export PATH=/usr/local/opt/coreutils/libexec/gnubin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Wireshark.app/Contents/MacOS
~~~