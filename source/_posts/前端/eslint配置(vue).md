---
title: eslint配置(vue)
date: 2018-02-01 15:16:46
categories: "前端之巅"
tags: '前端'
---

## eslint

为了整个团队的代码统一,我们使用了eslint

### vscode如何配置

VSCode拓展插件推荐（HTML、Node、Vue、React开发均适用）

在vscode添加 eslint 插件

~~~
// 添加进你的vscode的 setting.json

"eslint.autoFixOnSave": true,
"eslint.validate": [
    "javascript",{
        "language": "vue",
        "autoFix": true
    },"html",
    "vue"
],
~~~
