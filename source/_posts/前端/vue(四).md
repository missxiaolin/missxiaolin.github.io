---
title: vue 线上部署(四)
date: 2018-08-25 09:16:46
categories: "前端之巅"
tags: '前端'
---

## 前言

因为我们的项目是微服务话，对应的后端项目也越来越多，之前说过一个我们可以使用git来管理我们的项目[组件](https://missxiaolin.github.io/2018/07/17/%E5%89%8D%E7%AB%AF/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/)，今天我来说说如何做nginx一个域名部署多项目，毕竟以后还会有更多的项目。


### vue.js配置

1. config/index.js

修改 assetsPublicPath: '/vue-ceshi/'

~~~
build: {
	// Template for index.html
	index: path.resolve(__dirname, '../dist/index.html'),

	// Paths
	assetsRoot: path.resolve(__dirname, '../dist'),
	assetsSubDirectory: 'static',
	assetsPublicPath: '/',

	/**
	 * Source Maps
	 */

	productionSourceMap: true,
	// https://webpack.js.org/configuration/devtool/#production
	devtool: '#source-map',

	// Gzip off by default as many popular static hosts such as
	// Surge or Netlify already gzip all static assets for you.
	// Before setting to `true`, make sure to:
	// npm install --save-dev compression-webpack-plugin
	productionGzip: true,
	productionGzipExtensions: ['js', 'css'],

	// Run the build command with an extra argument to
	// View the bundle analyzer report after build finishes:
	// `npm run build --report`
	// Set to `true` or `false` to always turn it on or off
	bundleAnalyzerReport: process.env.npm_config_report
}
~~~

2. src/router/index.js文件修改

添加 base: '/vue-github-rank/'

~~~
import Vue from 'vue'
import Router from 'vue-router'


export default new Router({
  mode: 'history',
  base: 'vue-ceshi'
})
~~~

# nginx配置

~~~
server {
    listen       80;
    server_name  www.vuece.com;
    root /Users/mac/web/miss/vue-kong/dist;

    location / {
        # if (!-e $request_filename) {
            # rewrite ^(.*)$ /index.php?s=$1 last;
            # break;
        # }
        try_files $uri $uri/ /index.html$is_args$args;

    }

    location /vue-ceshi/ {
        root: /Users/mac/web/miss/vue-ceshi/dist;
        try_files $uri $uri/ /index.html$is_args$args;
        index index.html;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
~~~



