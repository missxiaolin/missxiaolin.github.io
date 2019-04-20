---
title: php-fpm监控
date: 2019-04-20 08:16:46
categories: "技术杂谈"
tags: ['mac','监控']
---

### 前置条件

需要安装 prometheus grafana php-fpm nginx

[链接](https://missxiaolin.github.io/2019/02/28/%E7%9B%91%E6%8E%A7/prometheus/)

### php-fpm、nginx配置请根据具体情况，修改路径。

php-fpm 修改配置文件重启

~~~
pm.status_path = /status
~~~

NGINX 修改配置文件重启

~~~
server {
        listen 9009;
        location ~ /status {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
}
~~~

预期

~~~
curl http://127.0.0.1:9009/status
pool:                 www
process manager:      dynamic
start time:           17/Apr/2019:17:23:51 +0800
start since:          254459
accepted conn:        6856
listen queue:         0
max listen queue:     0
listen queue len:     0
idle processes:       1
active processes:     1
total processes:      2
max active processes: 2
max children reached: 0
slow requests:        0
~~~


### 安装 php-fpm-exporter

~~~
git clone https://github.com/bakins/php-fpm-exporter && cd php-fpm-exporter && ./script/build
~~~

### 启动

~~~
./php-fpm-exporter.darwin.amd64 --addr=0.0.0.0:8088 --endpoint=http://127.0.0.1:9009/status &
~~~

### 预期效果

~~~
curl http://127.0.0.1:8088/metrics
# HELP phpfpm_accepted_connections_total Total number of accepted connections
# TYPE phpfpm_accepted_connections_total counter
phpfpm_accepted_connections_total 6859
# HELP phpfpm_active_max_processes Maximum active process count
# TYPE phpfpm_active_max_processes counter
phpfpm_active_max_processes 2
# HELP phpfpm_listen_queue_connections Number of connections that have been initiated but not yet accepted
# TYPE phpfpm_listen_queue_connections gauge
phpfpm_listen_queue_connections 0
# HELP phpfpm_listen_queue_length_connections The length of the socket queue, dictating maximum number of pending connections
# TYPE phpfpm_listen_queue_length_connections gauge
phpfpm_listen_queue_length_connections 0
# HELP phpfpm_listen_queue_max_connections Max number of connections the listen queue has reached since FPM start
# TYPE phpfpm_listen_queue_max_connections counter
phpfpm_listen_queue_max_connections 0
# HELP phpfpm_max_children_reached_total Number of times the process limit has been reached
# TYPE phpfpm_max_children_reached_total counter
phpfpm_max_children_reached_total 0
# HELP phpfpm_processes_total process count
# TYPE phpfpm_processes_total gauge
phpfpm_processes_total{state="active"} 1
phpfpm_processes_total{state="idle"} 1
# HELP phpfpm_scrape_failures_total Number of errors while scraping php_fpm
# TYPE phpfpm_scrape_failures_total counter
phpfpm_scrape_failures_total 0
# HELP phpfpm_slow_requests_total Number of requests that exceed request_slowlog_timeout
# TYPE phpfpm_slow_requests_total counter
phpfpm_slow_requests_total 0
# HELP phpfpm_up able to contact php-fpm
# TYPE phpfpm_up gauge
phpfpm_up 1
~~~

### prometheus 配置

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

  - job_name: 'php-fpm'

    scrape_interval: 3s
    metrics_path: "/metrics"
    static_configs:
    - targets: ['192.168.199.137:8088']

~~~

效果

http://localhost:9090/targets

<img src="http://missxiaolin.com/prometheus_20190420.png">

### Grafana 配置

~~~
https://grafana.com/dashboards/3901
~~~

http://localhost:3000

效果

<img src="http://missxiaolin.com/Grafana_20190420.png">


