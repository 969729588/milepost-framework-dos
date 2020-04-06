# RabbitMQ
> 本文档介绍SpringBoot对RabbitMQ的使用。
> 本框架基于org.springframework.boot:spring-boot-starter-amqp:2.1.0.RELEASE来使用RabbitMQ功能。

RabbitMQ有交换机和路由的概念，但是他只有Queue模式，没有ActiveMQ那样天然的Topic(广播)模式，要实现Topic(广播)模式可以使用[ActiveMqService](../../3guideForDevelopment/1commonDevelopment/14activeMQ.md)。
RabbitMQ主要用来完成SpringCloudBus相关的功能，框架基于RabbitMQ提供了RabbitMQService，实现如下几种收发消息的方式：
* 1(a)、向指定租户下的所有服务实例发消息
* 1(b)、向当前租户下的所有服务实例发消息
* 2(a)、向当前租户下，指定服务下的所有实例发消息
* 2(b)、向当前租户下，当前服务下的所有实例发消息
* 3、向当前租户下的指定实例发消息
* 4、向当前租户下，指定服务下的某个实例发消息，如这个服务是集群的(有多个实例)，则只会有一个实例接收到消息。


* RabbitMQ官网：https://www.rabbitmq.com/
* UI类服务和Service类服务都可以使用ActiveMQ功能，具体使用方法如下

## 1、配置RabbitMQ
```yaml
srping:
  rabbitmq:
    #rabbitmq服务器ip
    host: 192.168.223.136
    #rabbitmq端口
    port: 5672
    #rabbitmq用户
    username: admin
    #rabbitmq密码
    password: admin
```
## 2、发送消息
```java
@RestController
@RequestMapping("/rabbitMqProducer")
public class RabbitMqProducerController {

    private Logger logger = LoggerFactory.getLogger(getClass());

    @Autowired
    private RabbitMqService rabbitMqService;

    private String success = "发送成功";

    @GetMapping("/send2AllInstancesOfTheTenant")
    public Response<String> send2AllInstancesOfTheTenant(String tenant, String message) {
        Response<String> response;
        try {
            rabbitMqService.send2AllInstancesOfTheTenant(tenant, message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }

    @GetMapping("/send2AllInstancesOfCurrTenant")
    public Response<String> send2AllInstancesOfCurrTenant(String message) {
        Response<String> response;
        try {
            rabbitMqService.send2AllInstancesOfCurrTenant(message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }

    @GetMapping("/send2AllInstancesOfTheService")
    public Response<String> send2AllInstancesOfTheService(String serviceName, String message) {
        Response<String> response;
        try {
            rabbitMqService.send2AllInstancesOfTheService(serviceName, message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }


    @GetMapping("/send2AllInstancesOfCurrService")
    public Response<String> send2AllInstancesOfCurrService(String message) {
        Response<String> response;
        try {
            rabbitMqService.send2AllInstancesOfCurrService(message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }

    @GetMapping("/send2OneInstancesOfTheService")
    public Response<String> send2OneInstancesOfTheService(String serviceName, String message) {
        Response<String> response;
        try {
            rabbitMqService.send2OneInstancesOfTheService(serviceName, message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }

    @GetMapping("/send2TheInstance")
    public Response<String> send2TheInstance(String instanceId, String message) {
        Response<String> response;
        try {
            rabbitMqService.send2TheInstance(instanceId, message);
            response = ResponseHelper.createSuccessResponse(success);
        }catch (Exception e){
            logger.error(e.getMessage(), e);
            response = ResponseHelper.createExceptionResponse(e);
        }

        return response;
    }
}
```

## 3、接收消息
```java
package com.milepost.exampleService.rabbitMq.consumer;

import com.milepost.core.rabbitMq.RabbitMqListener;
import org.springframework.stereotype.Component;

/**
 * Created by Ruifu Hua on 2020/4/3.
 */
@Component
public class RabbitMqListenerImpl implements RabbitMqListener {

    @Override
    public void receiveByTenant2AllInstances(String message) {
        System.out.println("【receiveByTenant2AllInstances】:" + message);
    }

    @Override
    public void receiveByService2AllInstances(String message) {
        System.out.println("【receiveByService2AllInstances】:" + message);
    }

    @Override
    public void receiveByInstance(String message) {
        System.out.println("【receiveByInstance】:" + message);
    }

    @Override
    public void receiveByService2OneInstances(String message) {
        System.out.println("【receiveByService2OneInstances】:" + message);
    }
}
```

