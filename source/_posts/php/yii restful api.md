---
title: RESTful风格的API
date: 2017-04-26 15:16:46
categories: ["PHP","yii"]
tags: 'php'
---

### 什么是RESTful风格的API
对于各种客户端设备与服务端的通信，我们往往都通过API为客户端提供数据，提供某种资源。关于RESTful的概念，一查一大推，一两句也解释不清，姑且先按照我们通俗的理解：在众多风格、众多原则的API中，RESTful就是一套比较优秀的接口调用方式。

### 建立单独的应用程序
为了增加程序的可维护性，易操作性，我们选择新建一套应用程序，这也是为了和前台应用、后台应用区分开操作。有些人要嚷嚷了，为啥非得单独搞一套呢？如果你就单纯的提供个别的几个h5页面的话，那就没有必要了，但事实往往是客户端要升级啊，要增加不同的版本啊，这就需要我们不但要后端不仅要增加一套单独的应用程序，我们还要增加各种版本去控制。

下载yii高级模板 可以复制一个（frontend）或者 （backend） 到项目文件夹位置

修改common\config\bootstrap.php文件
~~~
Yii::setAlias('@api', dirname(dirname(__DIR__)) . '/api');
~~~

然后在environments目录的dev和prod里面增加api目录 同样可以复制（frontend）或者 （backend）

<img src="http://www.missxiaolin.com/yii%20api%E9%85%8D%E7%BD%AE.png">

nginx配置下这样就可以访问到api了

### 配置错误日志
~~~
'log' => [
    'traceLevel' => YII_DEBUG ? 3 : 0,
    'targets'    => [
        [
            'class'   => 'yii\log\FileTarget',
            'levels'  => ['error', 'warning'],
            'logFile' => "@runtime/logs/" . date('Y-m') . '/' . date('d') . '/' . date('H') . "-app.log",
        ],
    ],
],
~~~

### 美化路由

~~~
'urlManager' => [
    'enablePrettyUrl' => true,
    'showScriptName' => false,
    'enableStrictParsing'=>true,
    'rules' => [
    ],
],
~~~

### 下面我们在api目录下建立一个modules目录

~~~
├──────api
│       │
├───────modules
│        └────v1
│              ├─controllers
│              │      BaseController.php
               ├─Module.php
~~~

为了能让我们访问到这个模块不要忘记在main.php中添加下面代码

~~~
'modules' => [
    'v1' => [
        'class' => 'api\modules\v1\Module',
    ],
],
~~~

继续设置下路由就可以访问到了

~~~
'urlManager'   => [
    'enablePrettyUrl'     => true,
    'showScriptName'      => false,
    'enableStrictParsing' => true,
    'rules'               => [
        [
            'class'      => 'yii\rest\UrlRule',
            'controller' => [
                'v1/base/index',
            ],
        ],
        'GET  v1/base/index' => 'v1/base/index'
    ],
],
~~~

### 统一json返回
~~~
'response'     => [
    'class'         => 'yii\web\Response',
    'on beforeSend' => function ($event) {
        $response = $event->sender;
        $response->data = [
            'code'    => $response->getStatusCode(),
            'data'    => $response->data,
            'message' => $response->statusText,
        ];
        $response->format = yii\web\Response::FORMAT_JSON;
    },
],
~~~

### 当然，yii2还对该api封装了如下操作：

- GET /users: 逐页列出所有用户
- HEAD /users: 显示用户列表的概要信息
- POST /users: 创建一个新用户
- GET /users/123: 返回用户 123 的详细信息
- HEAD /users/123: 显示用户 123 的概述信息
- PATCH /users/123 and PUT /users/123: 更新用户123
- DELETE /users/123: 删除用户123
- OPTIONS /users: 显示关于末端 /users 支持的动词
- OPTIONS /users/123: 显示有关末端 /users/123 支持的动词

### 错误码




### 接着来看一看RESTFul API的一些最佳实践原则:
1.使用HTTP动词表示增删改查资源， GET：查询，POST：新增，PUT：更新，DELETE：删除
2.返回结果必须使用JSON
3.HTTP状态码，在REST中都有特定的意义：200，201,202,204, 400,401,403,500。比如401表示用户身份认证失败，403表示你验证身份通过了，但这个资源你不能操作。
4.如果出现错误，返回一个错误码。比如我通常是这么定义的：
- 200: OK。一切正常。
- 201: 响应 POST 请求时成功创建一个资源。Location header 包含的URL指向新创建的资源。
- 204: 该请求被成功处理，响应不包含正文内容 (类似 DELETE 请求)。
- 304: 资源没有被修改。可以使用缓存的版本。
- 400: 错误的请求。可能通过用户方面的多种原因引起的，例如在请求体内有无效的JSON 数据，无效的操作参数，等等。
- 401: 验证失败。
- 403: 已经经过身份验证的用户不允许访问指定的 API 末端。
- 404: 所请求的资源不存在。
- 405: 不被允许的方法。 请检查 Allow header 允许的HTTP方法。
- 415: 不支持的媒体类型。 所请求的内容类型或版本号是无效的。
- 422: 数据验证失败 (例如，响应一个 POST 请求)。 请检查响应体内详细的错误消息。
- 429: 请求过多。 由于限速请求被拒绝。
- 500: 内部服务器错误。 这可能是由于内部程序错误引起的。

5.API必须有版本的概念，v1，v2，v3
6.使用Token令牌来做用户身份的校验与权限分级，而不是Cookie。
7.url中大小写不敏感，不要出现大写字母
8.使用 - 而不是使用 _ 做URL路径中字符串连接。
9.有一份漂亮的文档~（很重要）

以上只是列出了RESTFul的部分实践原则，并非全部。 
