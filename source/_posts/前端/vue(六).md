---
title: vue (六)
date: 2020-03-26 09:16:46
categories: "前端之巅"
tags: '前端'
---

## 解决vue 在ios微信“复制链接”功能问题

### 解决vue 在ios微信“复制链接”复制不到动态地址的问题

这个问题在安卓上是完全没有问题的，可是到了iPhone上就彻底失效了。因为ios微信对vue路由中的#号识别问题。

我的解决办法是在初始进入项目时重新拼接项目地址，再重定向到拼接的地址去。

先获取到#号前的路由地址，再手动添加我们的#号和当前项目地址后缀：
在路由router.js里的beforeEach函数写

### 截取地址

~~~
// 重定向功能，为解决ios微信上复制链接功能不能复制到动态路由问题
// 获取地址前段部分，不算参数
 var replaceUrl = window.location.href.split('#')[0] + '#' + to.path;
 var index = 0; // 索引初始化
 // 给replaceUrl拼接参数
 for (var i in to.query) {
   // 判断是否等于第一个参数
   if (index == 0) {
     // 拼接地址第一个参数，添加“?”号
     replaceUrl += '?' + i + '=' + to.query[i]
   } else {
     // 拼接地址非第一个参数，添加“&”号
     replaceUrl += '&' + i + '=' + to.query[i]
   }
   index++; // 索引++
 }
~~~

### 重定向跳转

~~~
window.location.replace(replaceUrl); // 重定向跳转 
~~~

### 全部代码

~~~
router.beforeEach((to, from, next) => {	  
next();
// 重定向功能，为解决ios微信上复制链接功能不能复制到动态路由问题
// 获取地址前段部分，不算参数
var replaceUrl = window.location.href.split('#')[0] + '#' + to.path;
var index = 0; // 索引初始化
// 给replaceUrl拼接参数
for (var i in to.query) {
  // 判断是否等于第一个参数
  if (index == 0) {
    // 拼接地址第一个参数，添加“?”号
    replaceUrl += '?' + i + '=' + to.query[i]
  } else {
    // 拼接地址非第一个参数，添加“&”号
    replaceUrl += '&' + i + '=' + to.query[i]
  }
  index++; // 索引++
}
// console.log('test20190117：' + to.meta.title, replaceUrl);
window.location.replace(replaceUrl); // 重定向跳转
// 重定向功能------end
});
~~~