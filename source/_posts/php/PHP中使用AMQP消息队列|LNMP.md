---
title: PHP中使用AMQP消息队列|LNMP
date: 2018-07-26 16:30:46
categories: ["PHP"]
tags: 'php'
---

### RabbitMQ

MQ全称为Message Queue, 消息队列（MQ）是一种应用程序对应用程序的通信方法。MQ是消费-生产者模型的一个典型的代表，一端往消息队列中不断写入消息，而另一端则可以读取队列中的消息。

RabbitMQ是MQ的一种。下面详细介绍一下RabbitMQ的基本概念。

~~~
入队列： 

<?php  
//连接RabbitMQ  
$conn_args = array( 'host'=>'localhost' , 'port'=> '5672', 'login'=>'guest' ,   

'password'=> 'guest','vhost' =>'/');  
$conn = new AMQPConnection($conn_args);  
$conn->connect();  
//创建exchange名称和类型  
$channel = new AMQPChannel($conn);  
$ex = new AMQPExchange($channel);  
$ex->setName('direct_exchange_name');  
$ex->setType(AMQP_EX_TYPE_DIRECT);  
$ex->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);  
$ex->declare();  
//创建queue名称，使用exchange，绑定routingkey  
$q = new AMQPQueue($channel);  
$q->setName('queue_name');  
$q->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);  
$q->declare();  
$q->bind('direct_exchange_name', 'routingkey_name');  
//消息发布  
$channel->startTransaction();  
$message = json_encode(array('Hello World!','DIRECT'));  
$ex->publish($message, 'routingkey_name');  
$channel->commitTransaction();  
$conn->disconnect();  
?>  

取队列：

<?php  
//连接RabbitMQ  
$conn_args = array( 'host'=>'localhost' , 'port'=> '5672', 'login'=>'guest' , 'password'=> 'guest','vhost' =>'/');  
$conn = new AMQPConnection($conn_args);  
$conn->connect();  
//设置queue名称，使用exchange，绑定routingkey  
$channel = new AMQPChannel($conn);  
$q = new AMQPQueue($channel);  
$q->setName('queue_name');  
$q->setFlags(AMQP_DURABLE | AMQP_AUTODELETE);  
$q->declare();  
$q->bind('direct_exchange_name', 'routingkey_name');     
//消息获取  
$messages = $q->get(AMQP_AUTOACK) ;  
if ($messages){  
      var_dump(json_d ecode($messages->getBody(), true ));  
}  
$conn->disconnect();  
?>
~~~