# Spring Cloud Bus

> 本文档介绍框架对Spring Cloud Bus的使用。
> Spring Cloud Bus目前之前两种spring-cloud-starter-bus-amqp和spring-cloud-starter-bus-kafka，
本框架基于org.springframework.cloud:spring-cloud-starter-bus-amqp:2.1.0.RELEASE实现消息总线功能，其底层依赖RabbitMQ。

* spring-cloud-bus 官方网文档：https://cloud.spring.io/spring-cloud-bus/2.2.x/reference/html/
* spring-cloud-bus github地址：https://github.com/spring-cloud/spring-cloud-bus
* RabbitMQ官网：https://www.rabbitmq.com/

> 消息总线应用场景：
* 1、与spring-cloud-starter-config结合实现动态刷新配置。(以后有时间来实现)
* 2、与spring-cloud-stream结合实现微服务见调用的链路跟踪。见[SpringCloud Sleuth](../../3guideForDevelopment/2distributedDevelopment/12springCloudSleuth.md)
* 3、使SpringBoot事件驱动模型能够跨服务实例，具体见下面的代码：

事件发布方代码：
```java
package com.milepost.exampleApi.remoteEvent.event;

import org.springframework.cloud.bus.event.RemoteApplicationEvent;

/**
 * Created by Ruifu Hua on 2020/2/26.
 * 事件对象，要求所有服务使用相同的类，全类名要相同，
 */
public class EventObj extends RemoteApplicationEvent {

    public EventObj() {
    }

    public EventObj(Object source, String originService, String destinationService) {
        super(source, originService, destinationService);

    }

    public EventObj(Object source, String originService) {
        super(source, originService);
    }

}

```

```java
package com.milepost.exampleApi.remoteEvent.event;

import java.io.Serializable;

/**
 * Created by Ruifu Hua on 2020/2/26.
 * 事件源，与普通事件驱动模型的区别是实现Serializable接口，
 * 因为发送到RabbitMQ时需要序列化，
 * 同样要求所有服务使用相同的类，全类名要相同，
 */
public class EventSource implements Serializable {
    private String username;
    private String pwd;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "EventSource{" +
                "username='" + username + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }

    public EventSource(String username, String pwd) {
        this.username = username;
        this.pwd = pwd;
    }
}

```

```java
package com.milepost.exampleService.remoteEvent.event;

import com.milepost.exampleApi.remoteEvent.event.EventObj;
import org.springframework.cloud.bus.jackson.RemoteApplicationEventScan;
import org.springframework.context.annotation.Configuration;

/**
 * Created by Ruifu Hua on 2020/4/4.
 * 配置实现远程应用事件的事件对象
 */
@Configuration
@RemoteApplicationEventScan(basePackageClasses = EventObj.class)
public class BusConfig {

}

```

```java
package com.milepost.exampleService.remoteEvent.event;

import com.milepost.exampleApi.remoteEvent.event.EventObj;
import com.milepost.exampleApi.remoteEvent.event.EventSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.bus.BusProperties;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by Ruifu Hua on 2020/2/26.
 * 事件发布
 */
@RestController("remoteEventPublish")
@RequestMapping("/remoteEventPublish")
public class EventPublishController {

    //消息总线配置
    @Autowired
    private BusProperties busProperties;

    /**
     * 发布事件
     */
    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;

    /**
     * 测试事件发布
     *
     * @return
     */
    @GetMapping("testPublishEvent")
    public String testPublishEvent(EventSource eventSource){
        String originService = busProperties.getId();
        //"*:**";//表示所有服务，也可指定具体指，对应于spring.cloud.bus.id配置项，多个服务实例要保证此配置项唯一
        String destinationService = "example-ui:9980";
        EventObj eventObj = new EventObj(eventSource, originService, destinationService);
        applicationEventPublisher.publishEvent(eventObj);
        return "testPublishEvent";
    }
}

```

事件接收方代码：

事件接收方需要有事件发布方的EventObj、EventSource、BusConfig和下面的EventListen类，

```java
package com.milepost.exampleService.remoteEvent.event;

import com.milepost.exampleApi.remoteEvent.event.EventObj;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

/**
 * Created by Ruifu Hua on 2020/2/26.
 * 事件监听
 */
@Component("remoteEvent")
public class EventListen {

    /**
     * 监听EventObj事件
     * @param eventObj
     */
    @EventListener
    public void listenerForCustomEvent(EventObj eventObj){
        System.out.println(eventObj.getSource());
        System.out.println(eventObj.getOriginService());
        System.out.println(eventObj.getDestinationService());
    }
}

```

此场景不太使用，其优点是不需关注RabbitMQ的交换机、路由键、队列，缺点是增加了服务间代码耦合度。
与框架中提供的RabbitMqService相比不占优势，推荐使用RabbitMqService，此处的实例仅供参考。