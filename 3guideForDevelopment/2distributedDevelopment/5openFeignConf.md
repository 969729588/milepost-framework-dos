# OpenFeign参数配置

> 本文档主要介绍框架中如何配置OpenFeign的相关参数。

* 本文档的所有内容均来自[Spring Cloud OpenFeign官方文档](https://cloud.spring.io/spring-cloud-openfeign/2.1.x/single/spring-cloud-openfeign.html)
* OpenFeign的github地址是 https://github.com/OpenFeign/feign
* 本文档所讲述的内容是为了方便开发调试问题的，一般来讲不需要开发者来配置这些，使用框架默认配置即可。


## 1、请求和响应的压缩
```yaml
feign:
  compression:
    request: 
      enabled: true
      mime-types: text/xml,application/xml,application/json
      min-request-size: 2048
    response:
      enabled: true
```
SpringCloud默认关闭了请求和响应的压缩，本框架没有改变这种默认配置。


## 2、覆盖FeignClient默认的配置
见官方文档中的[1.2 Overriding Feign Defaults](https://cloud.spring.io/spring-cloud-openfeign/2.1.x/single/spring-cloud-openfeign.html#spring-cloud-feign-overriding-defaults)


## 3、手动创建一个FeignClient
见官方文档中的[1.3 Creating Feign Clients Manually](https://cloud.spring.io/spring-cloud-openfeign/2.1.x/single/spring-cloud-openfeign.html#_creating_feign_clients_manually)


## 4、多个@FeignClient指定相同的name(value)属性的解决方案

官方如是：<br>
If we want to create multiple feign clients with the same name or url 
so that they would point to the same server but each with a different custom configuration 
then we have to use contextId attribute of the @FeignClient in order to avoid name collision of these configuration beans.

配置@FeignClient注解的contextId属性，
在多个@FeignClient之间保证contextId属性值不重复即可，一般来讲配置为驼峰式类名即可。
不推荐通过spring.main.allow-bean-definition-overriding=true解决，因为这个属性默认值是false。

## 5、@FeignClient详细配置
```yaml
feign:
  client:
    config:
      default:
        #连接超时(ms)
        connectTimeout: 50000
        #读取超时(ms)
        readTimeout: 50000
        #重试策略，默认是feign.Retryer.NEVER_RETRY，即不重试，因为他与ribbon的重试冲突
        retryer: feign.Retryer.Default
        #拦截器
        requestInterceptors:
          - com.example.FooRequestInterceptor
          - com.example.BarRequestInterceptor
  hystrix:
    #开启hystrix后才可以使用@FeignClient注解的fallback属性和fallbackFactory属性
    enabled: true
```
其中 default 指向@FeignClient注解的name(value)属性值，default表示配置默认(全局)的FeignClient。


## 6、框架默认配置
```yaml
feign:
  hystrix:
    : true
```
由于feign的底层是ribbon在支持，所以推荐通过配置ribbon来改变feign，
关于feign，框架只配置了feign.hystrix.enabled=true，
没有其他的配置，关于Ribbon见[Ribbon参数配置](6ribbonConf.md)。

