---
title: 移动端自适应
date: 2018-02-06 15:16:46
categories: "前端之巅"
tags: '前端'
---

## 移动端如何做到自适应

### meta 标签

~~~
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no,minimal-ui">
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Expires" content="0" />
<!-- Makes your prototype chrome-less once bookmarked to your phone's home screen -->
<!-- iOS中Safari允许全屏浏览 -->
<meta name="apple-mobile-web-app-capable" content="no">
<!-- iOS中Safari顶端状态条样式 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<meta name="apple-mobile-web-app-title" content="724">
<!-- 忽略将数字变为电话号码, 忽略自动识别邮箱账号 -->
<meta name="format-detection" content="telephone=no, email=no">
<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320">
<!-- UC强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
~~~

Vue:将px转化为rem，适配移动端

### lib-flexible

我使用的是vue-cli+webpack，所以是通过npm来安装的

~~~
npm i lib-flexible --save
~~~

### 引入lib-flexible

在main.js中引入lib-flexible

~~~
import 'lib-flexible/flexible'
~~~

### 设置meta标签

~~~
<meta name="viewport" content="width=device-width, initial-scale=1.0">
~~~

### 安装px2rem-loader

~~~
npm install px2rem-loader --save-dev
~~~

### 配置px2rem-loader

utils.js中

~~~
exports.cssLoaders = function (options) {
  options = options || {}

  const cssLoader = {
    loader: 'css-loader',
    options: {
      sourceMap: options.sourceMap
    }
  }

  const postcssLoader = {
    loader: 'postcss-loader',
    options: {
      sourceMap: options.sourceMap
    }
  }

  // 加入
  const px2remLoader = {
    loader: 'px2rem-loader',
    options: {
      remUnit: 40
    }
  }

  // generate loader string to be used with extract text plugin
  function generateLoaders (loader, loaderOptions) {
    const loaders = options.usePostCSS ? [cssLoader, postcssLoader, px2remLoader] : [cssLoader, px2remLoader]

    if (loader) {
      loaders.push({
        loader: loader + '-loader',
        options: Object.assign({}, loaderOptions, {
          sourceMap: options.sourceMap
        })
      })
    }

    // Extract CSS when that option is specified
    // (which is the case during production build)
    if (options.extract) {
      return ExtractTextPlugin.extract({
        use: loaders,
        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
  }

  // https://vue-loader.vuejs.org/en/configurations/extract-css.html
  return {
    css: generateLoaders(),
    postcss: generateLoaders(),
    less: generateLoaders('less'),
    sass: generateLoaders('sass', { indentedSyntax: true }),
    scss: generateLoaders('sass'),
    stylus: generateLoaders('stylus'),
    styl: generateLoaders('stylus')
  }
}
~~~

### hotcss

如果不使用lib-flexible 我们也可以使用[hotcss](https://github.com/imochen/hotcss)

找到src/hotcss.js下载到本地，接下来配置我们的webpack……

我们找到webpack公用的配置项webpack.base.conf.js

~~~
entry: {
	app: './src/main.js'
}
改成
entry: ['./src/viewport.js','./src/main.js'],
~~~

大功告成！！！

