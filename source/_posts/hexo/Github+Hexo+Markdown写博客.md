---
title: Github+Hexo+Markdown写博客
date: 2017-03-28 10:27:46
categories: "git"
tags: 'git'
---

### Github+Hexo+Markdown写博客

『Markdown 写博客』互联网经历了由简入繁之后，现在进入了做减法的时代。以前人们写博客，总觉得编写博客的输入框不够丰富，能否支持更多富文本的东西呢？后来各种富文本编辑器出来，满足了一部分傻瓜用户的需求。慢慢的，一部分人发现富文本编辑器，有时候为了捣鼓一个格式『比方说有序列表嵌套』就能搞死人，完全不能把精力投入到内容本身上。于是一部分极客就发明了标记语法，例如Textile、Markdown等等。用这种方式写东西，可以将精力都放在内容本身上，最后可以使用脚本将标记语言转换生成html、pdf、word等富文本格式。后来，在各种标记语法PK过程中，Markdown胜出了。Github上的wiki全部也都是Markdown格式写的，还记得每个版本库的README.md吗？

### 原理

直接采用Markdown格式书写文章，然后通过脚本引擎，将Markdown解析为对应的html静态文件，加上一些润色CSS以及JavaScript，于是网页生成了。

### Hexo

[Hexo](https://hexo.io/) 是由台湾个人团队基于 Node JS 开发的一个快速、简洁且高效的博客框架。完全开源，源代码托管在 Github 上。安装后，通过其简单的命令就可以在本地快速的搭建起一个个人博客。官网上提供的文档全面且易阅读，非常容易上手。

### 环境准备

1. NodeJS安装
	
	访问官网进行安装 [nodejs.org](https://nodejs.org/en/)

2. hexo 安装
	
	~~~
	$ npm install hexo-cli -g    # 安装 hexo
	~~~

	注意 由于 node 的包管理工具npm默认采用的源在国内访问受限，因此，这里安装可能会卡壳或者报错。不幸中的万幸淘宝做了npm国内镜像，因此只需将npm的源换成[淘宝npm镜像](https://npm.taobao.org/)，问题可轻松解决。

	安装过程有任何因为，可参考[官方文档](https://hexo.io/zh-cn/docs/)

### 搭建本地静态博客

~~~
$ hexo init "My blog"          # 初始化项目，“My blog”为自定义项目名
$ cd "My blog"
$ npm install                # 安装依赖
$ hexo server                # 生成静态文件, 启动本地web服务器
~~~

### 写作博客

1. 新建一篇博客

~~~
$ hexo new "Article Name"
~~~

2. 写作

以Markdown格式写作

3. 生成静态HTML文件

~~~
$ hexo generate   #简写 hexo g
~~~

4. 运行博客，查看效果

~~~
$ hexo server    #简写 hexo s
~~~

### 部署到远程站点 – Github个人主页

1. 添加git作为Hexo远程部署的方式，并添加github仓库地址，作为部署的站点地址

  编辑_config.yml文件，添加如下信息

  ~~~
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    repo: https://github.com/$username/$username.github.io.git
    branch: master
  ~~~

2. 添加Git部署所依赖的库 hexo-deployer-git

~~~
$ npm install hexo-deployer-git --save
~~~

3. 添加.gitignore文件，忽略不必要的文件

~~~
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
~~~

4. 初始化仓库，并添加github远程源

~~~
$ cd "My blog"
$ git init
$ git add .
$ git commit -m "init blog" # 提交
$ git remote add origin https://github.com/$username/$username.github.io.git # 添加远程源
~~~

5. 部署到Github上

~~~
$ hexo deploy
~~~

至此，已经将博客部署到github个人主页上了，可以通过浏览器查看效果 http://$username.github.io
