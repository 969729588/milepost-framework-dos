# Ribbon参数配置

> 本文档主要介绍框架中如何配置ribbon的相关参数。

* 本文档的所有内容均来自[7. Client Side Load Balancer: Ribbon官方文档](https://cloud.spring.io/spring-cloud-netflix/2.2.x/reference/html/#spring-cloud-ribbon)
* Ribbon的github地址是 https://github.com/Netflix/ribbon/
* 本文档所讲述的内容是为了方便开发调试问题的，一般来讲不需要开发者来配置这些，使用框架默认配置即可。

spring-cloud-netflix官方文档：
* spring-cloud-netflix的官方文档见[Spring Cloud Netflix](https://cloud.spring.io/spring-cloud-netflix/2.2.x/reference/html/)
(Greenwich.RELEASE版本的SpringCLoud对应的Netflix是2.1.0.RELEASE，但是2.1.0.RELEASE版本的文档已经不在了。)
* spring-cloud-netflix的的github地址是 https://github.com/spring-cloud/spring-cloud-netflix

## 1、全局配置
```yaml
ribbon:
  #在同一个服务提供者中重试的次数，不包括第一次请求
  MaxAutoRetries: 0
  #切换其他服务提供者重试的次数，不包括第一次请求
  MaxAutoRetriesNextServer: 0
  #是否所有类型的请求都进行重试操作
  OkToRetryOnAllOperations: false
  #连接超时时间，单位ms，默认1000ms
  ConnectTimeout: 8000
  #读取超时时间，单位ms，默认1000ms
  ReadTimeout: 8000
```

## 2、配置特定的FeignClient
```yaml
FeignClientName:
  ribbon:
    #在同一个服务提供者中重试的次数，不包括第一次请求
    MaxAutoRetries: 0
    #切换其他服务提供者重试的次数，不包括第一次请求
    MaxAutoRetriesNextServer: 0
    #是否所有类型的请求都进行重试操作
    OkToRetryOnAllOperations: false
    #连接超时时间，单位ms，默认1000ms
    ConnectTimeout: 8000
    #读取超时时间，单位ms，默认1000ms
    ReadTimeout: 8000
```
其中“FeignClientName”是FeignClient注解的name(value)属性值。

## 3、框架的默认配置
Ribbon的重试只对Get请求有效，因为其他请求不一定是幂等(幂等：重复多次请求在服务端难造成的结果与一次请求的结果是一样的)的，
在开发过程中，不能保证所有Get请求中都没有更改数据的操作，所以本框架关闭了Ribbon的重试机制，并将超时时间配置成8000ms。
```yaml
ribbon:
  #在同一个服务提供者中重试的次数，不包括第一次请求
  MaxAutoRetries: 0
  #切换其他服务提供者重试的次数，不包括第一次请求
  MaxAutoRetriesNextServer: 0
  #是否所有类型的请求都进行重试操作
  OkToRetryOnAllOperations: false
  #连接超时时间，单位ms，默认1000ms
  ConnectTimeout: 8000
  #读取超时时间，单位ms，默认1000ms
  ReadTimeout: 8000
```


