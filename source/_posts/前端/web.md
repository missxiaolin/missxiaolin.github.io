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



### 算法

- 快速排序
- 选择排序
- 希尔排序
- 堆栈
- 队列
- 链表
- 递归	
