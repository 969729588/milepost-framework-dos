# Hystrix 熔断和降级

> 本文档描述框架对Hystrix的使用，包括服务熔断、服务降级。

* 本文档的所有内容均来自[Hystrix wiki Configuration 官方文档](https://github.com/Netflix/Hystrix/wiki/Configuration)
* Hystrix的github地址是 https://github.com/Netflix/Hystrix/
* 本文档所讲述的内容是为了方便开发调试问题的，一般来讲不需要开发者来配置这些，使用框架默认配置即可。

spring-cloud-netflix官方文档：
* spring-cloud-netflix的官方文档见[Spring Cloud Netflix](https://cloud.spring.io/spring-cloud-netflix/2.2.x/reference/html/)
(Greenwich.RELEASE版本的SpringCLoud对应的Netflix是2.1.0.RELEASE，但是2.1.0.RELEASE版本的文档已经不在了。)
* spring-cloud-netflix的的github地址是 https://github.com/spring-cloud/spring-cloud-netflix

> 微服务架构中，存在很多的服务单元，若一个单元出现故障，就容易因依赖关系而引发故障蔓延，
最终导致整个系统的瘫痪，这样的架构相较传统的架构更加不稳定，为了解决这种问题，
就产生了断路器等一系列的服务保护机制。在SpringCloud微服务架构采用Hystrix来实现这种保护机制，
防止分布式系统出现联动故障，提高整个系统的韧性，他提供了fail-fast机制和fallback方案，防止因资源被占满而影响其他服务。

Hystrix在SpringCloud微服务架构中提供了服务熔断和服务降级两种保护机制。
> 服务熔断和服务降级的核心功能就是自动进入熔断或者降级模式，<br>
即当一段时间内失败的调用太多时会自动进入fallback逻辑，<br>
服务熔断时不再执行真正的业务逻辑，服务降级时不再调用服务提供者。<br>
当调用恢复正常后，系统能从熔断或者降级模式中恢复到正常。<br>


## 1、服务熔断
### 1.1、简介
* 服务熔断是服务提供者中的服务链路保护机制，虽然也能在服务消费者中使用，但不推荐这样做。
* 熔断机制是应对雪崩效应的一种服务链路保护机制。
* 当扇出链路的某个服务提供者不可用或者响应时间太长时，Hystrix会进行服务熔断，
快速返回“错误”的响应信息给服务消费者。
* 当检测到该服务提供者响应正常后恢复调用链路。

### 1.2、使用场景
服务熔断功能是以Controller中的方法为单位来使用的。<br>
可以在服务提供者的Controller中的某一个或几个方法上使用服务熔断功能。<br>
适用于服务提供者的某一些比较重要的必须要求有返回值的接口。<br>
当服务提供者认为服务消费者比较重要而想提供自己服务的可靠性时就可以考虑使用服务熔断。<br>

### 1.3、使用方法
在服务提供者的启动类上标注@EnableHystrix注解，在Controller方法上标注@HystrixCommand注解并指定fallbackMethod。
```java
/**
 * HystrixCommand当方法出现没有被捕获的异常时，执行fallbackMethod，
 * @param param
 * @return
 */
@HystrixCommand(fallbackMethod = "test1_fb")
@ResponseBody
@GetMapping("/test1")
public Response<String> test1(@RequestParam("param") int param){
    Response<String> response = null;
    try {
        int i = 100/param;
        response = ResponseHelper.createSuccessResponse("调用成功，返回结果=" + i);
    }catch (Exception e){
        logger.error(e.getMessage(), e);
        response = ResponseHelper.createExceptionResponse(e);
    }
    return response;
}

/**
 * 永远不会进入才方法，因为test1把异常捕获了
 * @param param
 * @return
 */
public Response<String> test1_fb(int param){
    Response<String> response = ResponseHelper.createSuccessResponse("test1_fb，参数=" + param);
    return response;
}

@HystrixCommand(fallbackMethod = "test2_fb")
@ResponseBody
@GetMapping("/test2")
public Response<String> test2(@RequestParam("param") int param){
    int i = 100/param;
    Response<String> response = ResponseHelper.createSuccessResponse("调用成功，返回结果=" + i);
    return response;
}

/**
 * 当请求test2传入0时，会进入此方法。
 * @param param
 * @return
 */
public Response<String> test2_fb(int param){
    Response<String> response = ResponseHelper.createSuccessResponse("test2_fb，参数=" + param);
    return response;
}
```


## 2、服务降级
### 2.1、简介
* 服务熔断是服务消费者中的服务链路保护机制。
* 服务降级也是应对雪崩效应的一种服务链路保护机制。
* 当扇出链路的某个服务提供者不可用或者响应时间太长时，Hystrix会进行服务降级，
不会一直等待服务提供者返回数据，转而从服务消费者自己预先定义好的方法获取“错误”的响应信息。
(服务消费者也要@EnableHystrix注解开启熔断功能，否则会一直请求服务提供者。)
* 当检测到该服务调用提供者响应正常后恢复调用链路。

### 2.2、使用场景
服务降级功能是以@FeignClient标注的接口(以下简称FeignClient接口)为单位来使用的。<br>
要在某个FeignClient接口上使用服务降级功能需要提供这个FeignClient接口的实现类并实现其所有的方法，
这样能将服务降级的逻辑与FeignClient接口分离，降低代码耦合度。<br>
适用于服务消费者的某一些比较重要的必须要求有返回值的FeignClient接口。<br>
当服务消费者认为服务提供者不太可靠时就可以考虑使用服务降级。<br>

### 1.3、使用方法
配置@FeignClient的fallback属性或者fallbackFactory属性，
fallbackFactory对比fallback的优点是可以获取到异常信息，
此处为了同时配置了fallback和fallbackFactory，真正开发时候只能使用其中一个。

@FeignClient
```java
package com.milepost.exampleUi.openFeign.feignClient;

import com.milepost.api.vo.response.Response;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

/**
 * Created by Ruifu Hua on 2020/3/26.
 */
@FeignClient(contextId = "personFeignClient",
        name = "${info.app.service.name}",
        fallback = PersonFeignClientFallback.class,
        fallbackFactory = PersonFeignClientFallbackFactory.class)
public interface PersonFeignClient {

    @GetMapping("${info.app.service.prefix}/testOpenFeign/testFallbackFactory")
    Response<String> testFallbackFactory();

    @GetMapping("${info.app.service.prefix}/testOpenFeign/testFallback")
    Response<String> testFallback();
}
```

fallback类
```java
package com.milepost.exampleUi.openFeign.feignClient;

import com.milepost.api.vo.response.Response;
import com.milepost.api.vo.response.ResponseHelper;
import org.springframework.stereotype.Component;

/**
 * Created by Ruifu Hua on 2020/3/28.
 */
@Component
public class PersonFeignClientFallback implements PersonFeignClient{
    @Override
    public Response<String> testFallbackFactory() {
        Response<String> response = ResponseHelper.createFeignErrorResponse();
        response.setPayload("进入Fallback");
        return response;
    }

    @Override
    public Response<String> testFallback() {
        Response<String> response = ResponseHelper.createFeignErrorResponse();
        response.setPayload("进入Fallback");
        return response;
    }
}
```

fallbackFactory类
```java
package com.milepost.exampleUi.openFeign.feignClient;

import com.milepost.api.vo.response.Response;
import com.milepost.api.vo.response.ResponseHelper;
import feign.hystrix.FallbackFactory;
import org.springframework.stereotype.Component;

/**
 * Created by Ruifu Hua on 2020/3/26.
 */
@Component
public class PersonFeignClientFallbackFactory implements FallbackFactory<PersonFeignClient> {
    @Override
    public PersonFeignClient create(Throwable cause) {
        //使用FallbackFactory能获取异常信息，当断路器被触发打开是，会返回“Hystrix circuit short-circuited and is OPEN”。
        return new PersonFeignClient() {
            @Override
            public Response<String> testFallbackFactory() {
                Response<String> response = ResponseHelper.createFeignErrorResponse();
                response.setPayload(cause.getMessage());
                return response;
            }

            @Override
            public Response<String> testFallback() {
                Response<String> response = ResponseHelper.createFeignErrorResponse();
                response.setPayload(cause.getMessage());
                return response;
            }
        };
    }
}
```

## 3、服务熔断和服务降级的参数配置

### 3.1、配置详解
以下关于hystrix的配置，可以配置在服务消费者中来控制@FeignClient，也可以配置在服务提供者中来控制@HystrixCommand，效果是一样的。
```yaml
hystrix:
  command:
    default:
      execution:
        timeout:
          #开启线程执行超时，默认true
          enabled: true
        isolation:
          #hystrix资源隔离策略，THREAD(线程池)、SEMAPHORE(信号量)，默认THREAD
          strategy: THREAD
          thread:
            #hystrix线程执行超时时间，默认1000，在服务提供者中，ribbon的超时时间与hystrix的超时时间以小的那个为准，
            timeoutInMilliseconds: 1000
            #发生超时时，hystrix线程是否终止，默认true
            interruptOnTimeout: true
            #取消请求时，hystrix线程是否终止，默认false
            interruptOnCancel: false
      fallback:
        #开启fallback，影响@FeignClient和@HystrixCommand的fallback，默认true
        enabled: true
      circuitBreaker:
        #开启断路器功能(一段时间内失败的请求太多时@FeignClient和@HystrixCommand自动进入fallback逻辑的功能)，默认true
        enabled: true
        #滚动时间窗口内，失败请求数超过此数则断路器开启，默认20
        requestVolumeThreshold: 20
        #失败百分比，滚动时间窗口内，失败请求数这个百分比则断路器开启，默认50
        errorThresholdPercentage: 50
        #断路器开启后，超过此时间后尝试一次，如请求正常了，则关闭断路器，默认5000
        sleepWindowInMilliseconds: 5000
      metrics:
        rollingStats:
          #滚动时间窗口大小，默认10000
          timeInMilliseconds: 10000
```
其中 default 指向@HystrixCommand注解的方法名称，default表示配置默认(全局)的，@FeignClient只支持default。

### 3.2、框架默认配置
```yaml
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            #hystrix线程执行超时时间，默认1000，框架将其改成了10000，大于ribbon的超时时间，所以在服务消费者中ribbon先超时。
            timeoutInMilliseconds: 10000
```

