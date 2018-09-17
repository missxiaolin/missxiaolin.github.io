---
title: 基于supervisor秒级Laravel定时任务
date: 2018-09-17 16:30:46
categories: ["PHP",,"laravel"]
tags: 'php'
---

## 背景介绍

公司需要实现X分钟内每隔Y秒轮训某个接口，Linux自带的crontab貌似只精确到分钟，虽然可以到精确到秒，但是并不满足需求。

### 选型

公司项目都是 基于 Laravel 框架，所以这个没得选。守护进程用的 supervisor，看看这个家伙能不能满足我们的需求

## 代码

~~~
<?php

namespace App\Console\Commands\Task;

use Carbon\Carbon;
use Illuminate\Console\Command;

class Consume extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'task:consume
        
        {--id=      : 当前编号}
        {--max=     : 最大线程}
        {--sleep=   : 休眠多少毫秒}
        {--debug=   : 是否调试模式}
    ';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = '任务消费,轮训守护进程';

    /**
     * 任务编号
     *
     * @var int
     */
    protected $id;

    /**
     * 最大线程
     *
     * @var int
     */
    protected $max;

    /**
     * 休眠时间
     *
     * @var int
     */
    protected $sleep;

    /**
     * 是否开启debug
     *
     * @var bool
     */
    protected $debug;

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->id = $this->option('id') ?? '00';
        $this->max = $this->option('max') ?? 32;
        $this->sleep = $this->option('sleep') ?? 700;
        $this->debug = $this->option('debug') ?? false;

        if ($this->id > $this->max) {
            return true;
        }

        while (true) {
            $this->doRun();
            $this->wait($this->sleep);
        }
    }

    /**
     * 执行业务
     */
    public function doRun()
    {
        $lock = sprintf('task:%03d:%s', $this->id, time());
        $data = [
            'id' => $this->id,
            'max' => $this->max,
            'key' => $lock,
            'time' => (new Carbon)->format('Y-m-d H:i:s.u'),
        ];
        try {
            $data['message'] = 'Task Executed.';
            $this->logger($data);

        } catch (\Exception $ex) {
            $data['message'] = $ex->getMessage();
        }
    }

    /**
     * 日志
     * @param $message
     */
    protected function logger($message)
    {
        if ($this->debug) {
            $time = (new Carbon)->format('Y-m-d H:i:s.u');
            $this->line(array_get($message, 'message') . ' - ' . $time);
        }

        logger_instance('task-consume', $message);
    }

    /**
     * 毫秒
     * @param string $time
     */
    protected function wait($time)
    {
        $wait = $time * 1000;
        usleep($wait);
    }
}
~~~

## 进程守护

下面是supervisor的配置:

~~~
[program:task_dispatch]
command                 = /usr/local/opt/php71/bin/php artisan task:consume --id=%(process_num)02d --max=8
directory               = /Users/mac/web/miss/laravel-task
process_name            = %(program_name)s_%(process_num)02d
stdout_logfile          = /Users/mac/web/miss/laravel-task/storage/logs/supervisor.log
stdout_logfile_maxbytes = 10MB
stderr_logfile          = /Users/mac/web/miss/laravel-task/storage/logs/supervisor.log
stderr_logfile_maxbytes = 10MB
autostart               = true
autorestart             = true
numprocs                = 2
stopasgroup             = true
killasgroup             = true
~~~

### 日志

~~~
[2018-08-29 07:00:40] local.DEBUG: {
    "body": {
        "id": "01",
        "max": "8",
        "key": "task:001:1535526040",
        "time": "2018-08-29 07:00:40.685601",
        "message": "Task Executed."
    },
    "context": []
}  
[2018-08-29 07:00:40] local.DEBUG: {
    "body": {
        "id": "00",
        "max": "8",
        "key": "task:000:1535526040",
        "time": "2018-08-29 07:00:40.685635",
        "message": "Task Executed."
    },
    "context": []
}  
[2018-08-29 07:00:41] local.DEBUG: {
    "body": {
        "id": "01",
        "max": "8",
        "key": "task:001:1535526041",
        "time": "2018-08-29 07:00:41.462644",
        "message": "Task Executed."
    },
    "context": []
}  
[2018-08-29 07:00:41] local.DEBUG: {
    "body": {
        "id": "00",
        "max": "8",
        "key": "task:000:1535526041",
        "time": "2018-08-29 07:00:41.462730",
        "message": "Task Executed."
    },
    "context": []
} 
~~~

## 项目地址

[连接](https://github.com/missxiaolin/laravel-task)


