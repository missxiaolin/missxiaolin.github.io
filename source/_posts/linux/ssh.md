---
title: ssh 免密登录
date: 2018-02-23 9:52:46
categories: "linux"
tags: [linux']
---

## SSH 
### 免密码登录

1.首先生成id_rsa 和 id_rsa.pub

~~~
ssh-keygen -t rsa -C "limx@qq.com"
~~~

2.上传id_rsa.pub到服务器

3.将公钥加入到.ssh/authorized_keys

~~~
cd ~
cat id_rsa.pub >> .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
~~~