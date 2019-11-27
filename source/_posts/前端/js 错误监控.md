---
title: js 错误监控
date: 2019-11-30 15:16:46
categories: "前端之巅"
tags: '前端'
---

## 目的

更好的监控前端性能问题，排查线上bug，收集线上响应数据分析并做报警，及时发现Bug，提高Debug效率

### 背景

测试:出bug了

PM:这里怎么报错了客户:系统坏了

研发:我这没法复现，不是bug，是不是你们操作有问题

### 主要原因

前端面向终端用户，每个用户的情况千七八怪，在前端监控的情况下，很难定位到用户是什么场景发触发了什么问题也就是俗称的的"WHO did WHAT and get WHICH exception in WHICH environment?"

### 改进手段

建立数据采集-数据存储-统计分析-报告警告的监控系统 

### 方案

自研：

优点: 完全可控，数据在自己方

缺点: 研发成本高，功能没有成型产品齐全

基于sentry/betterJS(以前叫badJS)开源方案构建：

优点: 开源社区维护，功能齐全可靠，数据可控

缺点: 目前暂无 在子级页面我会后面做调研分析

购买成型产品服务如Fundebug：

优点: 功能强大包含录屏 小程序支持，接入方便

缺点: 费用高昂，有本地部署和远端服务两种模式，数据不可控

备注 阿里也有成型的解决方案 叫ONEAPM

### 捕获错误的常见的几种方法

~~~
GlobalEventHandlers.onerror
windows下面的全局error事件程序，当有javaSript脚本运行错误或者资源<img>、<script>加载失败时，都会触发Event接口的error事件，也能被window.addEventListener捕获到
~~~

### 两种写法

~~~
window.onerror = function(message, source, lineno, colno, error) {
    console.log(message)//字符串错误信息
    console.log(source)//发生错误的脚本
    console.log(lineno)//发生错误的行号
    console.log(colno)//发生错误的列号
    console.log(error)//Erroe对象
     
    return true//将代码错误定格在捕获阶段
}
 
 
window.addEventListener('error', (msg, url, row, col, error) => {
    console.log(
        msg, url, row, col, error
    );
    return true;
}, true);
~~~

注意

- window.onerror只有在返回true时，异常才不会向上抛出,原理是冒泡机制。而且window.onerror是无法捕获到网络异常的，因为网络请求异常不会事件冒泡，虽然能在捕获阶段捕获到，但是无法判断HTTP的状态码，需要配合服务端才能排查。
- 我们平时写window.onerror的时候，最好写在最顶端，以免前面的错误脚本阻断了。同时在vue或者react框架中，他们有自身的错误捕获机制，所以会掩盖window.onerror，比如vue的Vue.config.errorHandler。想详细了解看官网文档即可。

try...catch...finally

这种方式应该是我们平时用的最多的，具体功能想必大家也应该都很清楚，将我们需要检测的代码包裹在try和catch中，当发生语法错误或者其他错误时，就会从catch里捕获到，而不管前端发生了什么，最后finally里的代码总会执行。

下面的代码会怎么执行，大家可以思考一下

~~~
try {
  try {
    throw new Error("oops");
  }
  catch (ex) {
    console.error("inner", ex.message);
    throw ex;
  }
  finally {
    console.log("finally");
    return;
  }
}
catch (ex) {
  console.error("outer", ex.message);
}
~~~

特别注意


try...catch只能捕获到运行时的非异步错误，而语法错误和异步错误就捕捉不到。

还有一点就是try..catch比较消耗性能，能少用的最好。

unhandledrejection

有些错误try..catch和windows.onerror也是无能为力的，比如说Promise实例从pending转变为rejected时，如果加了catch就会被捕获到，但要是没有加，那么继续抛出就会Uncaught(in promise) Error

比如下面代码就捕获不到错误

~~~
window.onerror = function(message, source, lineno, colno, error) {
    console.log(message)//字符串错误信息
    console.log(source)//发生错误的脚本
    console.log(lineno)//发生错误的行号
    console.log(colno)//发生错误的列号
    console.log(error)//Erroe对象
     
    return true//将代码错误定格在捕获阶段
}
 
 
// 调用Promise.reject类方法
Promise.reject('promise error');
// 在工厂方法中调用reject方法
new Promise((resolve, reject) => {
    reject('promise error');
});
// 在工厂方法或then回调函数中抛异常
new Promise((resolve) => {
    resolve();
}).then(() => {
    throw 'promise error'
});
~~~

我们可以通过下面的方法监听到

~~~
window.addEventListener('unhandledrejection', function (event) {
  // ...your code here to handle the unhandled rejection...
 
 
  // Prevent the default handling (such as outputting the
  // error to the console
 
 
  event.preventDefault();
});
~~~

但是这方法的兼容性有点失望

What the heck is "Script error"?

‘Script error’ 这个错误是比较常见的，一般通过windows.onerror捕获到后基本确实就是哪个script文件有问题，而且这个文件还是跨域的，既然是跨域导致信息不全，所以首先要解决的就是跨域问题，关于跨域方面的前端解决方案可以看我这篇文章《前端常见跨域方案汇总

)》；

对于script标签，我们还需要额外配置一个参数crossOrigin

<script src='www.example.com' crossorigin></script>

至于服务端，常用的就是

了解sourceMap


对于这个功能的讲解，看阮大神的讲解

)是最适合不过的了，当然了解其基本设计思路也是很重要的，SourceMap其实就是一个信息文件，存储着源文件的信息及源文件与处理后文件的映射关系。我们平时vue或者react项目开发中，通过webpack配置在测试环境中默认开启生成SourceMap,出现错误能够及时重现原代码，但是正式环境我们一般是不会将SourceMap文件发布上去的，但是正式环境的代码一般都是压缩过的，所以如果报错了，一般是很难 定位到原代码的位置，这时候优秀的错误上传功能，以及平台处理错误分析就显得尤为重要了。下图就是大概的设计思路。


Navigator.sendBeacon()存在的意义

平时我们很常见网页卡顿或者直接崩溃，一般是通过window 对象的 load 和 beforeunload 事件实现了网页崩溃的监控；具体看这篇文章，Logging Information on Browser Crashes

实现代码

~~~
window.addEventListener('load', function () {
  sessionStorage.setItem('good_exit', 'pending');
  setInterval(function () {
     sessionStorage.setItem('time_before_crash', new Date().toString());
  }, 1000);
});


window.addEventListener('beforeunload', function () {
  sessionStorage.setItem('good_exit', 'true');
});


if(sessionStorage.getItem('good_exit') &&
  sessionStorage.getItem('good_exit') !== 'true') {
  /*
     insert crash logging code here
 */
  alert('Hey, welcome back from your crash, looks like you crashed on: ' + sessionStorage.getItem('time_before_crash'));
}
~~~

但是上面的编码模式存在不友好的问题，当我们尝试在卸载页面前通过上传服务器数据，为了延迟页面卸载，需要通过同步XMLHttpRequest 发送数据或者创建一个几秒的循环来延迟卸载。这样的处理可想而知不是很友好，这时候sendBeacon()就横空出世了，很简单的实现向服务器发送数据，同时又不会影响下一个页面的加载，具体如下面简单的代码实现。

~~~
window.addEventListener('unload', logData, false);
 
 
function logData() {
    navigator.sendBeacon("www.youAddress.com", analyticsData);
}
~~~