---
title: node linux安装
date: 2017-11-28 11:36:46
categories: ["node"]
tags: '前端之巅'
---

### 创建

~~~
#!/usr/bin/env bash
version='7.9.0'
wget https://npm.taobao.org/mirrors/node/v${version}/node-v${version}-linux-x64.tar.gz
tar xzf node-v${version}-linux-x64.tar.gz
mkdir -p /usr/local/nodejs/${version}
cp -rf node-v${version}-linux-x64/* /usr/local/nodejs/${version}

ln -sf /usr/local/nodejs/${version}/bin/node /usr/local/bin/node
ln -sf /usr/local/nodejs/${version}/bin/npm /usr/local/bin/npm

echo checking nodejs:
node -v
echo checking npm:
npm -v
# 设置镜像源
npm config set registry=http://registry.npm.taobao.org
~~~

### 权限
~~~
chmod +x ins_node.sh
~~~

### 运行

~~~
./ins_node.sh
~~~