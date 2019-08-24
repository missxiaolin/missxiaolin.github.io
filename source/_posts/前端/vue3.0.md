---
title: vue3.0
date: 2019-08-24 15:16:46
categories: "前端之巅"
tags: '前端'
---

## vue-cli3.0结合lib-flexible、px2rem实现移动端适配，完美解决第三方ui库样式变小问题

公司最近做的一个移动端项目从搭框架到前端开发由我独立完成，以前做移动端适配用的媒体查询，这次想用点别的适配方案，然后就采用了vue-cli3.0结合lib-flexible、px2rem实现移动端适配的方案，开发过程中也遇到一些坑，自己选的方案自己填坑吧。以下记录我的项目框架搭建及填坑方案。

### 第一部分：项目中引入lib-flexible

1、项目中安装lib-flexible

~~~
npm install lib-flexible --save
~~~

2、在项目的入口main.js文件中引入lib-flexible

~~~
import 'lib-flexible'
~~~

### 第二部分：使用postcss-px2rem自动将css中的px转换成rem

1、安装postcss-px2rem (一定看完文章再安装不然你会哭o(╥﹏╥)o)

~~~
npm install postcss-px2rem --save-dev
~~~

2、项目配置postcss

项目开始我是在vue.config.js（项目创建完初始是没有这个js文件的，需要自己手动创建）中配置的，上代码

~~~
module.exports = {
    css: {
        loaderOptions: {
          postcss: {
            // 这是rem适配的配置  注意： remUnit在这里要根据lib-flexible的规则来配制，如果您的设计稿是750px的，用75就刚刚好。
             plugins: [
              require("postcss-px2rem")({
                remUnit: 75
          })
        ]
      }
    }
}
~~~

始的适配方案就完成了，然后可以直接在css或.vue文件中写已px为单位的样式了，到浏览器会自动转为rem。

因为前期项目安排不是太赶，ui给的设计图也相对简单，所以自己写了按钮组件之类的，适配还挺好，觉得自己棒棒哒~

直到ui的后续设计图出现时间插件，然后就引入了vant的组件库。

放了一个简单的cell组件，npm run serve项目跑起来，我想哭o(╥﹏╥)o，组件中的样式都变小了，F12看了一下果然组件的样式也都被转换成了rem。

变小的主要原因是第三库 css一依据 data-dpr="1" 时写的尺寸

~~~
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
~~~

这时我们使用的flexible引入时 data-dpr不是一个写死了的，是一个动态的；就导致这个问题

然后就各种查解决方案，网络上给的解决方案一个是改写第三方库的样式，还有一个就是让flexible不编译第三方库的文件（忽略ui组件库的样式文件）。

#### 解决方案：

使用postcss-px2rem-exclude，网上好多说用这个方法不起作用，经过一个下午的折腾才发现是使用方法不对，我的错误方法就不跟你们说了，直接来正确的。

首先，需要卸载项目中的postcss-px2rem。

~~~
npm  uninstall postcss-px2rem --save-dev
~~~

其次，安装postcss-px2rem-exclude

~~~
npm  install postcss-px2rem-exclude --save
~~~

最后是配置exclude选项，需要注意的是这个配置在vue.config.js

~~~
css: {
    modules: false, // 启用 CSS modules
    extract: true, // 是否使用css分离插件
    sourceMap: false, // 开启 CSS source maps?
    loaderOptions: {
        postcss: {
            // 这是rem适配的配置  注意： remUnit在这里要根据lib-flexible的规则来配制，如果您的设计稿是750px的，用75就刚刚好。
             plugins: [
                require("postcss-px2rem-exclude")({
                    remUnit: 75,
                    exclude: /node_modules|folder_name/i
                })
            ]
        }
    } // css预设器配置项
},
~~~

正确的配置位置是项目根目录下的postcss.config.js文件，如果你的项目没有生成这个独立文件，就需要在你的package.js里设置。

~~~
postcss.config.js

module.exports = {
  plugins: {
    autoprefixer: {},
    "postcss-px2rem-exclude": {
      remUnit: 75,
      exclude: /node_modules|folder_name/i
    }
  }
};
~~~