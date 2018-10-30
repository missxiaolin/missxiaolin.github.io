---
title: Hexo更换主题 (此篇以安装NexT为例)
date: 2017-04-04 09:42:18
categories: "Hexo"
tags: 'Hexo'
---

### 进入到主题文件目录

```Shell
cd themes
```

默认主题 <u>landscape</u> 目录结构:

<img src="http://www.missxiaolin.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-24%2009.39.24.png">



### clone `hexo-theme-next` 并改名为 `next`

~~~Shell
git clone https://github.com/iissnan/hexo-theme-next.git next
~~~

当然你也可以clone 自己喜欢的Hexo主题Git地址,只需要将主题文件复制到themes这个文件内就可以了:

```Shell
git clone theme_url.git
```



### 打开Hexo根目录下的 `_config.yml` 站点配置文件（tips:注意空格!）,将 `theme` 的landscape修改为刚才clone的 `next` 

~~~yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
~~~

同时设置一下站点的语言、时区，其他的配置可根据自己喜好更改：

~~~Yaml
language: zh-Hans
timezone: Asia/Urumqi
~~~

执行一下 `hexo clear` 清除缓存。



### 验证主题是否存在异常问题

~~~shell
hexo s --debug
~~~

会提示如下信息，访问 `http://localhost:4000/` :

~~~shell
INFO Hexo is running at http://localhost:4000/. Press Ctrl+C to stop
~~~



### 主题具体配置

通过更改 `themes/next/_config.yml` 文件对next主题进行具体的配置：

~~~Yaml
# next主题可通过修改Scheme选项获得3中不同的外观，默认使用的是Muse：
scheme: Mist

# 添加个人头像：
avatar: http://avatar.url

# 菜单栏menu配置，根据自己喜好开启注释:
menu:
  home: /
  categories: /categories
  about: /about
  archives: /archives
  tags: /tags
  #sitemap: /sitemap.xml
  commonweal: /404.html
~~~

玩法很多并集成了常用的第三方服务，可以参考[Next主题官网](http://theme-next.iissnan.com/getting-started.html#select-scheme)

**Enjoy it !**