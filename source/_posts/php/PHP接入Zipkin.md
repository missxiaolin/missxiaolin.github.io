---
title: PHP接入Zipkin
date: 2018-02-09 15:30:46
categories: ["PHP"]
tags: 'php'
---

### Zipkin

zipkin为分布式链路调用监控系统，聚合各业务系统调用延迟数据，达到链路调用监控跟踪。

### 安装

~~~
docker run --name zipkin -d -p 9411:9411 openzipkin/zipkin
~~~

### RPC协议接入

~~~
namespace App\Common\Zipkin;
 
 
use Xin\Thrift\ZipkinService\Options;
 
use Xin\Traits\Common\InstanceTrait;
 
use Exception;
 
use Zipkin\Propagation\TraceContext;
 
 
class ZipkinClient
 
{
 
    use InstanceTrait;
 
 
    public $context;
 
 
    public $options;
 
 
    public function __construct()
 
    {
 
        // if (defined('IS_MEMORY_RESIDENT') && IS_MEMORY_RESIDENT === true) {
 
        //     throw new Exception('常驻内存模式下，不允许使用单例对象作为调用链存储方式');
 
        // }
 
    }
 
 
    public function setOptions(Options $options)
 
    {
 
        $context = TraceContext::create(
 
            $options->traceId,
 
            $options->spanId,
 
            $options->parentSpanId,
 
            $options->sampled
 
        );
 
 
        $this->context = $context;
 
        $this->options = $options;
 
    }
 
 
    public function getOptions()
 
    {
 
        return $this->options;
 
    }
 
 
    public function getContext()
 
    {
 
        return $this->context;
 
    }
 
}
~~~

### 客户端实现__call方法如下

~~~
public function __call($name, $arguments)
{

    $options = end($arguments);

    if (!$options instanceof Options) {

        $options = ZipkinClient::getInstance()->getOptions();

        $arguments[] = $options;

    }

    return $this->client->$name(...$arguments);

}
~~~

### 服务端实现

~~~
namespace App\Thrift\Services;
 
 
use App\Common\Zipkin\ZipkinClient;
 
use App\Core\Zipkin\Tracer;
 
use App\Thrift\Services\Impl\ImplHandler;
 
use Phalcon\Di\Injectable;
 
use Xin\Thrift\ZipkinService\ThriftException;
 
use Zipkin\Tracing;
 
 
abstract class Handler extends Injectable
 
{
 
    /** @var  ImplHandler */
 
    protected $impl;
 
 
    public function __call($name, $arguments)
 
    {
 
        if (empty($this->impl)) {
 
            throw new ThriftException([
 
                'code' => 0,
 
                'message' => '微服务Handler没有设置其实现'
 
            ]);
 
        }
 
 
        /** @var Tracer $tracing */
 
        $tracer = di('tracer');
 
 
        $spanName = $this->impl . '@' . $name;
 
        $options = array_pop($arguments);
 
        list($child_trace, $options) = Tracer::getInstance()->newChild($tracer, $spanName, $options);
 
        ZipkinClient::getInstance()->setOptions($options);
 
        $arguments[] = $options;
 
 
        try {
 
            $result = $this->impl::getInstance()->$name(...$arguments);
 
        } finally {
 
            $child_trace->finish();
 
            $tracer->flush();
 
        }
 
 
        return $result;
 
    }
 
}
~~~

### 项目地址

[zipkin](https://github.com/missxiaolin/laravel-thrift-zipkin-project)



