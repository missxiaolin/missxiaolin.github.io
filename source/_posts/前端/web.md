---
title: web
date: 2018-03-18 15:16:46
categories: "前端之巅"
tags: '前端'
---

## ES6

### ES6模块化如何使用，开发环境如何打包

1. 基本语法

~~~
export default {
	a: 100
}

export function fn1(){
	return 1;
}
~~~

2. 开发环境配置(babel转换)

npm install --save-dev babel-core babel-prest-es2015 babel-preset-latest 创建.babelrc文件

~~~
{
  "presets": [
    "es2015"
  ],
  "plugins": [
    "transform-decorators-legacy"
  ]
}
~~~

打包 webpack 

npm install webpack babel-loder

### vdom

[snabbdom](https://github.com/snabbdom/snabbdom)

#### vdom为什么使用diff算法

- dom操作是“昂贵”的，尽量减少dom操作
- 找出dom必须跟新的节点来跟新，其他不跟新
- 这个“找出”的过程，需要diff

### 判断是否手机端

~~~
if ((navigator.userAgent.match(/(iPhone|iPad|Android|Windows Phone|baidu Transcoder|BlackBerry)/i))) {
    var sToMatch = String(window.document.location.href);
    var reEs = /http(.*?):\/\/(.*?).xxxxxx\/(.*)/;

    var wapDomain;
    if (reEs.test(sToMatch)) {
        wapDomain = sToMatch.match(reEs)[3];
        if (wapDomain == "")
            wapDomain = sToMatch.match(reEs)[1];
    }
    if (wapDomain != "")
        location.replace("//xxxxxxxxxx/" + wapDomain);
}
~~~

### pc浏览器判断

~~~
(function($, window, document,undefined){
  if(!window.browser){
      
    var userAgent = navigator.userAgent.toLowerCase(),uaMatch;
    window.browser = {}
      
    /**
     * 判断是否为ie
     */
    function isIE(){
      return ("ActiveXObject" in window);
    }
    /**
     * 判断是否为谷歌浏览器
     */
    if(!uaMatch){
      uaMatch = userAgent.match(/chrome\/([\d.]+)/);
      if(uaMatch!=null){
        window.browser['name'] = 'chrome';
        window.browser['version'] = uaMatch[1];
      }
    }
    /**
     * 判断是否为火狐浏览器
     */
    if(!uaMatch){
      uaMatch = userAgent.match(/firefox\/([\d.]+)/);
      if(uaMatch!=null){
        window.browser['name'] = 'firefox';
        window.browser['version'] = uaMatch[1];
      }
    }
    /**
     * 判断是否为opera浏览器
     */
    if(!uaMatch){
      uaMatch = userAgent.match(/opera.([\d.]+)/);
      if(uaMatch!=null){
        window.browser['name'] = 'opera';
        window.browser['version'] = uaMatch[1];
      }
    }
    /**
     * 判断是否为Safari浏览器
     */
    if(!uaMatch){
      uaMatch = userAgent.match(/safari\/([\d.]+)/);
      if(uaMatch!=null){
        window.browser['name'] = 'safari';
        window.browser['version'] = uaMatch[1];
      }
    }
    /**
     * 最后判断是否为IE
     */
    if(!uaMatch){
      if(userAgent.match(/msie ([\d.]+)/)!=null){
        uaMatch = userAgent.match(/msie ([\d.]+)/);
        window.browser['name'] = 'ie';
        window.browser['version'] = uaMatch[1];
      }else{
        /**
         * IE10
         */
        if(isIE() && !!document.attachEvent && (function(){"use strict";return !this;}())){
          window.browser['name'] = 'ie';
          window.browser['version'] = '10';
        }
        /**
         * IE11
         */
        if(isIE() && !document.attachEvent){
          window.browser['name'] = 'ie';
          window.browser['version'] = '11';
        }
      }
    }
  
    /**
     * 注册判断方法
     */
    if(!$.isIE){
      $.extend({
        isIE:function(){
          return (window.browser.name == 'ie');
        }
      });
    }
    if(!$.isChrome){
      $.extend({
        isChrome:function(){
          return (window.browser.name == 'chrome');
        }
      });
    }
    if(!$.isFirefox){
      $.extend({
        isFirefox:function(){
          return (window.browser.name == 'firefox');
        }
      });
    }
    if(!$.isOpera){
      $.extend({
        isOpera:function(){
          return (window.browser.name == 'opera');
        }
      });
    }
    if(!$.isSafari){
      $.extend({
        isSafari:function(){
          return (window.browser.name == 'safari');
        }
      });
    }
  }
})(jQuery, window, document);
~~~



### 算法

- 快速排序
- 选择排序
- 希尔排序
- 堆栈
- 队列
- 链表
- 递归	
