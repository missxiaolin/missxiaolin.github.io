---
title: reviewboard 使用手册
date: 2020-03-20 7:52:46
categories: "linux"
tags: ['linux']
---

## review code模式

eviewboard支持两种review code模式，一种是在code没有commit之前提交diff文件进行review，叫做pre-commit review；另一种则是在code commit之后，由工具自动根据提交的版本号生成diff文件，并形成一条新的review request，这种模式也叫做post-commit review。

### review流程

post-commit review模式（提交到gitlab之后，推荐使用此模式）

创建评审请求

直接点击某一次提交记录即可生成评审请求

<img src="http://missxiaolin.com/image2020-3-19_14-46-40.png">

填写必要信息，点击Publish即可发布该评审请求，发布之后，对应的审核人员会收到相应的邮件提醒。

<img src="http://missxiaolin.com/image2020-3-19_14-59-17.png">

### 审核评审

审核人员点击评审请求进入评审界面

<img src="http://missxiaolin.com/image2020-3-19_15-35-28.png">

点击Diff可查看相应的代码变化并添加评论，需要注意的是绿色对话框下部的勾选项"Open an Issue"，如果勾选，说明你认为这是个严重问题，否则说明你填写的只是些严重程度比较低的建议。

<img src="http://missxiaolin.com/image2020-3-19_15-38-34.png">

可以直接点击Ship It直接通过，也可以点击Review填写最终评审意见，如果评审通过，就勾选Ship It，否则不勾选。

<img src="http://missxiaolin.com/image2020-3-19_15-41-57.png">
<img src="http://missxiaolin.com/image2020-3-19_15-42-24.png">

如果评审不通过，申请人可以点击Update中Update Diff更新diff文件再次发起评审。

diff文件可以通过git diff commit_id1 commit_id2> /path/a.diff生成

<img src="http://missxiaolin.com/image2020-3-19_15-45-26.png">

### 查看评审流程

点击Reviews可查看评审完整流程，同时可对评审人员的建议进行回复评论。

<img src="http://missxiaolin.com/image2020-3-19_15-51-3.png">
<img src="http://missxiaolin.com/image2020-3-19_15-55-25.png">

### 关闭评审请求

评审通过后，申请人可以点击Close中Submitted关闭该请求。

<img src="http://missxiaolin.com/image2020-3-19_15-57-53.png">

pre-commit review模式（提交到gitlab之前）

手动上传diff文件进行review

先使用git diff commit_id1 commit_id2> /path/a.diff生成diff文件，然后在界面手动上传diff文件，其他流程和post-commit review模式类似。

<img src="http://missxiaolin.com/image2020-3-19_16-8-55.png">

使用RBT工具进行review

首先要安装python2.7，然后下载如下安装包，并依次解压、运行python setup.py install进行安装。

https://pypi.python.org/packages/source/a/argparse/argparse-1.4.0.tar.gz

https://pypi.python.org/packages/source/s/six/six-1.8.0.tar.gz

https://pypi.python.org/packages/source/R/RBTools/RBTools-0.7.5.tar.gz

运行如下命令，查看是否安装成功：rbt -v



配置git选项reviewboard.url为你的RB服务器访问链接：

git config --global  reviewboard.url http://review.enmonster.com


配置Git commit钩子脚本（如不需要自动提交代码评审功能，此步可以跳过）：

cd /gitpath/.git/hooks/

touch post-commit

chmod +x post-commit



cat .git/hooks/post-commit

#!/bin/sh

rbt post -g -p --username=xxx --password=xxx --target-people=xxx --repository=xxx

参数解释：

-g：根据git commit日志自动构造RB Review request的summary信息和description信息

-p：自动构造和发布；如未指定，会构造一个RB Review request页面，但不会发布

--target-people：指定代码评审Review request的人员

--target-groups：指定代码评审Review request的Groups；

--repository：指定仓库名称

--tracking-branch：指定工作分支；若不指定，默认为origin/master分支（适用于Git）

可以通过rbt post --help查看其他选项使用方法



如果没有配置Git commit钩子脚本，也可以在commit后，利用rbt手动提交RB Review request，比如：

指定REVISION的修改记录提交代码评审：rbt post REVISION

指定(STARTREV,STOPREV)区间的修改记录提交代码评审：rbt post STARTREV STOPREV





