---
title: vue 线上部署(四)
date: 2018-08-25 09:16:46
categories: "前端之巅"
tags: '前端'
---

## 前言

因为我们的项目是微服务话，对应的后端项目也越来越多，之前说过一个我们可以使用git来管理我们的项目[组件](https://missxiaolin.github.io/2018/07/17/%E5%89%8D%E7%AB%AF/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/)，今天我来说说如何做nginx一个域名部署多项目，毕竟以后还会有更多的项目。


### vue.js配置

1. config/index.js

修改 assetsPublicPath: '/vue-github-rank/'

<img src="http://oni42o7kl.bkt.clouddn.com/vue-nginx1.png">

2. src/router/index.js文件修改

添加 base: '/vue-github-rank/'

<img src="http://oni42o7kl.bkt.clouddn.com/vue-nginx2.png">

# nginx配置

<img src="http://oni42o7kl.bkt.clouddn.com/vue-nginx3.png">



