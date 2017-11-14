---
title: git指南
date: 2017-10-15 9:22:46
categories: "git"
tags: 'git'
---

### git

git branch 获取当前分之

git checkout issues/#94 切换分之

git pull --rebase origin issues/#94 拉去远程分之

git status git checkout . 当前分之不要的删除

git push 提交

git pull --rebase origin issues/#74 拉去远程特性分之


### 分之提交

git status

git add .

git commit -m "默认值修改 (#refs 132)"

git commit -m "登录修改(refs #74)" comit

git rebase --continue

git push 提交

git pull --rebase origin issues/#74 push不成功

git push  提交


### 解决冲突

git rebase --abort

git pull --rebase origin dev 远程拉去dev

both_modified

git add .

git rebase --contine

git add .

git rebase --continue

git push -f origin issues/#159

### 解决冲突2

切换到dev分支, 将远程的dev最新内容拉取下来


git checkout dev

git pull origin dev

2. 将dev最新代码合并到特性分支

git checkout issues/#ID

git merge dev (这里合并时,就会终端,需要手动合并)

both_modified就是冲突的文件

3. 手动解决冲突,  解决一个文件,就添加一个(git add /path/to/file)

所有冲突文件解决完后, 提交(可以用图形 或者命令行 git commit)

4. 推送到远程
git push origin issues/#ID

###git后悔药
git commit --amend

### git修改提交的用户名和Email 

git config --global user.name "username"

### 解决冲突



### 暂存
git stash

git stash pop


###修改不上传文件

- vi .git/info/exclude