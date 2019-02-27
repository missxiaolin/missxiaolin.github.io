---
title: iterm2和oh my zsh
date: 2019-02-26 15:16:46
categories: "技术杂谈"
tags: ['mac']
---

## 前景

之前一直使用 Mac OS 自带的终端，用起来虽然有些不太方便，但总体来说还是可以接受的，是有想换个终端的想法，然后今天偶然看到一个终端利器 iTerm2，发现真的很强大，也非常的好用，按照网上配置了主题什么的，还是有些坑的，这边再记录下，以便后面查阅。

### brew 安装

~~~
curl -LsSf http://github.com/mxcl/homebrew/tarball/master | sudo tar xvz -C/usr/local --strip 1
或
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
~~~

### item2

mac内置的terminal 存在的意义就跟ie 存在的意义一样；
是为了用来安装 iterm2 替换 mac 的 terminal ；

~~~
brew cask install iterm2
~~~

command+空格 输入 iterm ;
启动了这样一个黑乎乎的窗口；
实在是吃藕；

<img src="http://missxiaolin.com/1551166887351.jpg">

### 安装wget

很多时候我们需要使用命令行下载文件；
这时候就需要使用 wget ；

~~~
brew install wget
~~~

### oh my zsh

默认的 bash 比较难用；
有个叫 zsh 的；
全称是 Z shell ；
因为Z是最后一个字母；
因此大家称之为终极shell；
但是 zsh 有比较难配置；
还好有一个叫 oh my zsh 的；

~~~
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
~~~

安装好了 zsh 顺手增加 brew 的镜像设置；

~~~
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc source ~/.zshrc
~~~

再修改配置项换个主题;

~~~
vim ~/.zshrc
~~~

把 ZSH_THEME 改为 ys

<img src="http://missxiaolin.com/1551167161598.jpg">

重新加载文件

~~~
source ~/.zshrc
~~~

### 安装插件

1.incr 效果就是可以快速的提示并补全目录

~~~
mkdir ~/.oh-my-zsh/plugins/incr 
wget http://mimosa-pudica.net/src/incr-0.2.zsh -O ~/.oh-my-zsh/plugins/incr/incr.plugin.zsh
~~~

2.zsh-autosuggestions  自动补全以前输入过的命令

~~~
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
brew install zsh-autosuggestions
source /usr/local/share/zsh-autosuggestions/zsh-autosuggestions.zsh
~~~

修改配置项

~~~
vim ~/.zshrc

修改下面plugins配置
plugins=( git incr zsh-autosuggestions )
~~~

但是此时的 iterm2 中复制命令特别卡；
就跟个慢动作样；
比如说我复制个：

~~~
brew cask install google-chrome
~~~

~~~
vim ~/.zshrc

增加以下配置
pasteinit() {
  OLD_SELF_INSERT=${${(s.:.)widgets[self-insert]}[2,3]}
  zle -N self-insert url-quote-magic # I wonder if you'd need `.url-quote-magic`?
}

pastefinish() {
  zle -N self-insert $OLD_SELF_INSERT
}
zstyle :bracketed-paste-magic paste-init pasteinit
zstyle :bracketed-paste-magic paste-finish pastefinish
~~~

重新载入配置文件

~~~
source ~/.zshrc
~~~






















