---
title: mysql监控
date: 2019-05-15 08:16:46
categories: "技术杂谈"
tags: ['mac','监控']
---

### 前置条件

需要安装 prometheus grafana php-fpm nginx

[链接](https://missxiaolin.github.io/2019/02/28/%E7%9B%91%E6%8E%A7/prometheus/)

### 安装 mysqld_exporter

~~~
https://github.com/prometheus/mysqld_exporter/releases
~~~

### 启动 mysqld_exporter

~~~
make
export DATA_SOURCE_NAME='user:password@(hostname:3306)/'
./mysqld_exporter <flags>
~~~

### 配置prometheus

~~~
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['127.0.0.1:9090']

  - job_name: 'mysql'

    scrape_interval: 3s
    metrics_path: "/metrics"
    static_configs:
    - targets: ['192.168.199.137:9104']
~~~

### 下载 grafana-dashboards

导入 MySQL_Overview.json 

### 效果图

<img src="http://missxiaolin.com/mysqld_20190515.png">