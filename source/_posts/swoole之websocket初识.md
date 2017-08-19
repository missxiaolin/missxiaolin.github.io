---
title: swoole之websocket初识
date: 2017-05-07 15:16:46
categories: ["PHP","yii"]
tags: 'php'
---

之前我们是不是用融云api写过一个聊天[融云即时通讯在线聊天（yii2.0）](https://missxiaolin.github.io/2017/04/05/%E8%9E%8D%E4%BA%91%E5%8D%B3%E6%97%B6%E9%80%9A%E8%AE%AF%E5%9C%A8%E7%BA%BF%E8%81%8A%E5%A4%A9/)

接下来为我们的聊天打基础，先了解下websocket

什么是websocket呢？
websocket是一个协议，它仅仅就是一个协议而已，跟我们所了解的http协议、https协议、ftp协议等等一样，都是一种单纯的协议。

相对于Http这种非持久连接而言，websocket协议是一种持久化连接，它是一种独立的，基于TCP的协议。基于websocket，我们可以实现客户端和服务端双向通信。

websocket是双向持久连接，客户端和服务端只需要第一次建立连接即可实现双向通信，说到这里，你肯定明白我们学习websocket要做什么了。没错，基于websocket，我们可以做一些通讯，推送相关的服务。

swoole内置的websocket服务器，异步非阻塞多进程，牛逼的swoole！

说了很多废话了，上代码

### 创建websocket服务器

~~~
<?php
namespace console\controllers;

use yii\console\Controller;

class WebSocketController extends Controller
{
    public function actionTest()
    {
        echo '启动websocket' . PHP_EOL;
        $serv = new \swoole_websocket_server("127.0.0.1", 9501);
        $serv->set([
            'worker_num' => 1,
        ]);
        $serv->on('open', [$this, 'onOpen']);
        $serv->on('message', [$this, 'onMessage']);
        $serv->on('close', [$this, 'onClose']);
        $serv->start();
    }

    /**
     * 客户端与服务端建立连接的时候将触发该回调,回调的第二个参数是swoole_http_request对象，包括了http握手的一些信息，比如GET\COOKIE等
     * @param $serv
     * @param $request
     */
    public function onOpen(\swoole_websocket_server $serv, $request)
    {
        echo "server: handshake success with fd{$request->fd}.\n";
    }

    /**
     * 这个是服务端收到客户端信息后回调，在该回调内我们调用了swoole_websocket_server::push方法向客户端推送了数据，注意哦，push的第一个参数只能是websocket客户端的标识
     * @param $serv
     * @param $frame
     */
    public function onMessage(\swoole_websocket_server $serv, $frame)
    {
        // 循环当前的所有连接，并把接收到的客户端信息全部发送
        foreach ($serv->connections as $fd) {
            $serv->push($fd, $frame->data);
        }
    }

    public function onClose(\swoole_websocket_server $serv, $fd)
    {
        echo "client {$fd} closed.\n";
    }


}
~~~

1. onOpen回调：客户端与服务端建立连接的时候将触发该回调，回调的第二个参数是swoole_http_request对象，包括了http握手的一些信息，比如GET\COOKIE等
2. onMessage回调：这个是服务端收到客户端信息后回调，在该回调内我们调用了swoole_websocket_server::push方法向客户端推送了数据，注意哦，push的第一个参数只能是websocket客户端的标识

在来看看页面js
~~~
<div>
    <textarea name="content" id="content" cols="30" rows="10"></textarea>
    <button onclick="send();">发送</button>
</div>
<div class="list" style="border: solid 1px #ccc; margin-top: 10px;">
    <ul id="ul">
    </ul>
</div>

<script>
    var ws = new WebSocket('ws://127.0.0.1:9501');
    ws.onopen = function(event) {
//        ws.send('This is websocket client.');
    };
    ws.onmessage = function(event) {
        var data = event.data;
        var ul = document.getElementById('ul');
        var li = document.createElement('li');
        li.innerHTML = data;
        ul.appendChild(li);
    };
    ws.onclose = function(event) {
        console.log('Client has closed.\n', event);
    };

    function send() {
        var obj = document.getElementById('content');
        var content = obj.value;
        ws.send(content);
    }
</script>
~~~

其实做到这里已经是一个简易的聊天室了
<img src="http://oni42o7kl.bkt.clouddn.com/websocket%E5%88%9D%E8%AF%86.png" />