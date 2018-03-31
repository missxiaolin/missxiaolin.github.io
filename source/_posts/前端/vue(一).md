---
title: vue 引入jquery (一)
date: 2017-04-04 15:16:46
categories: "前端之巅"
tags: '前端'
---

### Vue如何引入Jquery
1.首先在package.json里加入

~~~
dependencies:{
 "jquery" : "^2.2.3"
}
~~~

然后 nmp install

2.在webpack.base.conf.js里加入

~~~
var webpack = require("webpack")
~~~

3.在module.exports的最后加入

~~~
plugins: [
 new webpack.optimize.CommonsChunkPlugin('common.js'),
 new webpack.ProvidePlugin({
     jQuery: "jquery",
     $: "jquery"
 })
]
~~~

4.然后一定要重新 run dev

5.在main.js 引入就ok了

~~~
import $ from 'jquery'
~~~

### vue实现的整理流程

- 第一步：解析模板成render函数
- 响应式开始监听
- 首页渲染，显示页面，且绑定依赖
- data属性变化，触发rerender（vdom重新path）





