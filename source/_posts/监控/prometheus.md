---
title: prometheus grafana 安装
date: 2019-02-28 08:16:46
categories: "技术杂谈"
tags: ['mac','监控']
---

## prometheus

随着容器技术的迅速发展，Kubernetes 已然成为大家追捧的容器集群管理系统。Prometheus 作为生态圈 Cloud Native Computing Foundation（简称：CNCF）中的重要一员,其活跃度仅次于 Kubernetes, 现已广泛用于 Kubernetes 集群的监控系统中。本文将简要介绍 Prometheus 的组成和相关概念，并实例演示 Prometheus 的安装，配置及使用，以便开发人员和云平台运维人员可以快速的掌握 Prometheus。

### pull镜像

~~~
docker pull prom/prometheus:latest
~~~

### 运行

~~~
docker run -p 9090:9090 \
-v /tmp/prometheus-data:/prometheus-data \
prom/prometheus
~~~

### 如果要映射配置文件

~~~
docker run -p 9090:9090 -v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml \
       -v /tmp/prometheus-data:/prometheus-data \
       prom/prometheus
~~~

### 访问

http://127.0.0.1:9090

<img src="http://missxiaolin.com/WechatIMG2276.png">

计算实例，指标可以从http://127.0.0.1:9090/metrics中找

~~~
prometheus_target_interval_length_seconds{quantile="0.99"}
~~~

或者

~~~
count(prometheus_target_interval_length_seconds)
~~~

### brew 安装 prometheus

~~~
brew install prometheus

==> Downloading https://homebrew.bintray.com/bottles/prometheus-2.7.1.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring prometheus-2.7.1.sierra.bottle.tar.gz
==> Caveats
When used with `brew services`, prometheus' configuration is stored as command line flags in
  /usr/local/etc/prometheus.args

Example configuration:
  echo "--config.file ~/.config/prometheus.yml" > /usr/local/etc/prometheus.args


To have launchd start prometheus now and restart at login:
  brew services start prometheus
Or, if you don't want/need a background service you can just run:
  prometheus
==> Summary
🍺  /usr/local/Cellar/prometheus/2.7.1: 18 files, 93.6MB
~~~

### brew 安装 grafana

~~~
brew install grafana

==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
No changes to formulae.

==> Downloading https://homebrew.bintray.com/bottles/grafana-6.0.0.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring grafana-6.0.0.sierra.bottle.tar.gz
Warning: grafana dependency icu4c was built with a different C++ standard
library (libc++ from clang). This may cause problems at runtime.
==> Caveats
To have launchd start grafana now and restart at login:
  brew services start grafana
Or, if you don't want/need a background service you can just run:
  grafana-server --config=/usr/local/etc/grafana/grafana.ini --homepath /usr/local/share/grafana --packaging=brew cfg:default.paths.logs=/usr/local/var/log/grafana cfg:default.paths.data=/usr/local/var/lib/grafana cfg:default.paths.plugins=/usr/local/var/lib/grafana/plugins
==> Summary
🍺  /usr/local/Cellar/grafana/6.0.0: 3,121 files, 164.8MB
~~~

然后访问http://127.0.0.1:3000

默认账号密码admin admin

### docker 安装 grafana

~~~
docker pull grafana/grafana
~~~

启动

~~~
docker run -d -p 3000:3000 -v /Users/dokcer/grafana/data:/data grafana/grafana
~~~

