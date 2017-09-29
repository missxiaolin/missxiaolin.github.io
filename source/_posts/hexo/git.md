---
title: git指南
date: 2017-09-29 9:27:46
categories: "git"
tags: 'git'
---


# deep-gotgit

> ##### git config 配置文件的三种级别

```
# 版本库级别:
git config -e
# 全局(用户主目录下):
git config -e --global
# 系统级:
git config -e --system
```

> ##### 添加修改配置文件参数

```
git config a.b something
```

> ##### 不删除工作目录文件，仅删除暂存区的文件

```
git rm --cached cached.txt
```

> ##### 跳过使用暂存区域直接提交

```
git commit -a -m "不使用git add 直接提交"
```

> ##### 查找当前工作区的Git版本库位置

```
$ pwd
/Applications/www/My blog/source
$ git rev-parse --git-dir
/Applications/www/My blog/.git
```

> ##### 显示工作区根目录

```
git rev-parse --

/Applications/www/My blog
```

> ##### 显示工作区根目录的相对目录

```
$ cd source/_posts
git rev-parse --show-prefix
source/_posts/
```

> ##### 重新修改最新的提交, 改正作者和提交者的错误信息

```
$ git commit --amend --allow-empty --reset-author
## 参数说明:
    # --amend 对刚刚的提交进行修补,不产生新的提交
    # --allow-empty 使空白提交被允许
    # --reset-author 将Author提交者的ID同步修改,重置AuthorDate信息
```

> ##### 精简查看日志

```
git log --pretty=oneline
```

> ##### 精简格式的状态

```
git status -s
```

> ##### 比较差异

```shell
# 工作区与提交暂存区(stage)对比:
git diff
# 工作区与HEAD(当前工作分支)对比:
git diff HEAD
# 提交暂存区与版本库文件比较:
git diff --cached
# 比较里程碑 A 和里程碑 B
git diff A B
# 比较工作区和里程碑 A
git diff A
```

> ##### 逐词比较

```shell
git diff --word-diff
```

> ##### 文件追溯，找出谁修改过文件，由谁引入

```shell
git blame README
# 只查看第2行后面的3行
git blame -L 2,+3 README
```

> ##### 显示暂存区的目录树

```
git ls-file -s
```

> ##### 通过Git日志重置master

```
$ git reflog show master| head -5
9cb12d3 master@{0}: reset: moving to 9cb12d3
f1fbdca master@{1}: reset: moving to HEAD^
cba158e master@{2}: commit: does master follow this new commit?
f1fbdca master@{3}: commit: which version checked in?
9f47925 master@{4}: commit: welcaome Push

# 重置master为两次改变之前的值
$ git reset --hard master@{2}
```

> ##### 撤回上一次commit提交, 工作区内容不改变

```
git reset --soft HEAD^

# 参数解释:
--soft  
不删除工作空间改动代码，撤销commit，不撤销git add . 
--mixed 
意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的
--hard
删除工作空间改动代码，撤销commit，撤销git add . 
```

> ##### 查看当前 HEAD 的指向

```
$ cat .git/HEAD
ref: refs/heads/master
```

> ##### 挽救分离头指针

```
# 1.在分离头指针的分支上查看并记录下提交 ID
git:(04f430b) $ cat .git/HEAD
04f430b1ff24534b9fd8a8ccf0e96b6df1f4179f

# 2.切换回 master 分支进行 merge 将分离的修改内容与 master 进行合并
$ git checkout master
$ git merge 04f430b1ff24534b9fd8a8ccf0e96b6df1f4179f
```

> ##### commit 后，想回到之前的状态，放弃最新的提交

```
git reset --soft HEAD^
# 提交日志也被抛弃...
```

> ##### 将暂存区的文件撤出(add 后的文件)

```
# 撤出所有暂存区文件
$ git reset
# 撤出指定文件
$ git reset HEAD path/to/workspace/welcome.txt
```

> ##### 清除本地修改

```
$ git st -s
M README.md
?? abc.html

$ git checkout -- README.md
```

> ##### 删除本地多余文件或目录

```
# 确保万一，先查询会删除哪些文件
git clean -nd
# 开始强制删除文件或目录
git clean -fd
```

> ##### 创建里程碑

```
git tag -m "Say bye-bye to all previous practice." old_pratice
```

> ##### 查看暂存区文件目录

```
git ls-files
```

> ##### 查看历史版本的文件列表

```
$ git ls-files --with-tree=HEAD^
README.md
detacaed-commit.txt
new-welcome.txt
welcome.txt
```

> ##### 查看历史版本中尚存的删除文件的内容

```shell
$ git cat-file -p HEAD^:welcome.txt
hello. git
Nice to meet you
hello echo
```

> ##### 将(版本库追踪的)本地文件的变更全部记录到暂存区中

```shell
git add -u
```

> ##### 从历史( HEAD^ 前一次提交)恢复指定文件

```Shell
# 以下3种命令均可实现
$ git cat-file -p HEAD~1:welcome.txt > welcome.txt
$ git show HEAD~1:welcome.txt > welcome.txt
$ git checkout HEAD~1 --welcome.txt
```















