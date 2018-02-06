---
title: 移动端自适应
date: 2018-02-06 15:16:46
categories: "前端之巅"
tags: '前端'
---

## 移动端如何做到自适应

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

