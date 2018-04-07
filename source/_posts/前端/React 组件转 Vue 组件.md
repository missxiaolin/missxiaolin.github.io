---
title: React 组件转 Vue 组件
date: 2018-04-17 16:16:46
categories: "前端之巅"
tags: '前端'
---

## 背景

基于目前React和Vue比较火，开发react-to-vue工具的目的是为了进一步提高组件的可复用用性，让组件复用不仅仅局限在一个框架里面

## 简介

对于react-to-vue工具，转化的是基本的react component，而不是全部的react应用。而基本react component的定义更多是基于props和state来渲染的组件，其中也可以包括发请求。
本文先介绍两个框架的组件共性和不兼容的地方，再介绍react-to-vue的使用和原理。在实际业务中，陆金所100多个的react基础业务组件，react-to-vue可以转化90%以上，变成vue组件。

### 盘点两个框架的组件共性

1. props

| 框架 |  | 说明 |
| ------ || ------ |
| React || 通过propTypes来定义属性名和属性类型,defaultProps用来设置默认值 |
| Vue || 通过添加props属性 |

~~~
// react

FrontendMagazine.propTypes = {
	name:PropTypes.string
}

FrontendMagazine.defaultProps = {
	name:'FrontendMagazine'
}

// vue
{
    name:'frontend-magazine',
    props:{
		name:{
           type:String,
           default:'FrontendMagazine'
		}
	}
}
~~~

2. 组件自有状态

| 框架 |  | 说明 |
| ------ || ------ |
| React || 在初始化的时候，通过this.state = {xxx}来设置 |
| Vue || 通过data 返回函数来设置值，不同于react的state，vue是响应式 |

3. 生命周期

虽然生命周期名不一样，但是差不多有对应的

4. 处理事件

| 框架 |  | 说明 |
| ------ || ------ |
| React || 相应的事件都加到了组件的实例方法上 |
| Vue || 设计上比较好，处理事件都加在一个methods对象下面，方便查找,更直观 |

~~~
// react

class FrontendMagazine{
    clickme () {
		// xxxx
	}
}

// vue

{
    name:'frontend-magazine',
    methods:{
        clickme (){ 
        	// xxx
        }
	}
}
~~~

5. 组件错误捕获

| 框架 |  | 说明 |
| ------ || ------ |
| React || componentDidCatch |
| Vue || errorCaptured |

6. jsx语法

react是基于jsx来写的，对于vue来说，虽然在好多场景下，可以使用template来写，但是vue也完全支持jsx语法的，对于本工具，也只是把react的jsx语法转换成vue支持的jsx

两个框架不兼容的地方

react在最新版本里面，有flagments的支持，允许根节点返回多个节点，目前没有看到vue支持的，还有就是在设计react组件的时候，使用了高阶，对于本工具，也是不支持的

### react-to-vue工具

安装及使用

~~~
# install
npm install - g react-to-vue
# usage
Usage:rtv [options]file(react component)
# demo
rtv demo.js
~~~

原理步骤
- 对于输入的文件首先使用babylon来解析，生成ast
- 如果文件是typescript，会去掉相应的ts描述
- 对ast进行遍历，首先提取propTypes和defaultProps
- 根据组件类型，处理函数组件和类组件
- 在类组件里面，需要转换生命周期，state等信息
- 最后根据提取到的信息拼接成vue组件，通过prettier-eslint来美化

### 如何同时写开源的react和vue组件

如果你的组件想要被大家开源使用，作者这里有一个小提议，可以边写react组件，边试着转化成vue组件，如果转化有问题，试着改代码，尽可能跨框架支持，这样你写的组件肯定可以在react和vue项目中同时使用。








