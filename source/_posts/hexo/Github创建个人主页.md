---
title: Github创建个人主页
date: 2017-03-28 9:27:46
categories: "git"
tags: 'git'
---

### Github创建个人主页

『互联网上的发声』曾几何时，我们还在用51、163等等写博客，后来发现这些平台要么倒闭了，要么不自由，不支持markdown语法写博客，等等诸多不便。那我们自己买VPS，买域名搭建博客写？什么？代价太大，太繁琐，还要备案。我只是想有一个发声的地方，就这么难么？『Duang』Github 推出一个个人主页服务。

### 创建个人主页

GitHub 为每一个用户分配了一个二级域名 $username.github.io，用户为自己的二级域名创建主页很容易，只要在个人托管空间下创建一个名为 $username.github.io的版本库，向其master分支提交网站静态页面即可，其中网站首页为index.html。

1. 创建个人主页版本库

<img src="http://www.missxiaolin.com/github-create-repo.png">

2. 克隆版本库到本地

~~~
$ git clone git@github.com:username/username.github.io.git
$ cd username.github.io/
~~~

3. 在版本库根目录中创建文件index.html作为首页。

~~~
<html>
  <head>
  <title>hello</title>
</head>
<body>
  <h1>Hello world!</h1>
</body>
</html>
~~~

4. 提交并推送到远程Github上。

~~~
$ git add index.html
$ git commit -m "Homepage init."
$ git push origin master
~~~

5. 至此，个人主页已经生成，访问网址查看结果： http://username.github.io/