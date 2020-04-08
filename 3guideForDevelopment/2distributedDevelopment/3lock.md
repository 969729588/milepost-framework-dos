# 分布式锁

> 本文档介绍框架中的分布式锁功能的原理和使用方法，包括分布式调度锁和分布式同步锁两种。

> 本框架的分布式锁功能基于redis实现。

* 使用分布式锁的前提是服务能正常连接redis。
* 在微服务架构下，一个服务可以部署多个实例，多实例之间是集群关系。
* 本框架自动为每一个实例设置了实例ID，其值是${eureka.instance.ip-address}:${multiple-tenant.tenant}:${spring.application.name}:${server.port}，请开发者不要设置实例ID。

## 1、功能概述
### 1.1、实例角色服务
判断一个实例的角色是master还是slave。
> 此功能的规则
* 1.1.1、框架将一个服务的多个实例分为master和slave两种角色，多个实例中只有一个master，其他的都是slave。
* 1.1.2、如果redis中存在 以当前服务名称为key，以当前实例ID为value 的k-v对（以下将这个k-v对称为实例角色标记），则认为这个实例就是所有实例中的master，否则认为他是slave。
* 1.1.3、在redis中，一个服务对应一个实例角色标记，一个服务的多个实例共用一个实例角色标记。
* 1.1.4、在redis中实例角色标记不是永久有效的，它的过期时长是${scheduler-lock.heartbeat-expiration-duration-in-seconds}。

> 此功能的实现流程
* 1.1.5、一个服务的每个实例启动完成后，都会尝试将实例角色标记存入redis中。
* 1.1.6、在存入之前如果发现redis中已经存在了这个服务的实例角色标记，则证明当前服务已经存在master了，当前实例应该是一个slave。
* 1.1.7、如果实例成功将实例角色标记存入了redis中，则证明当前服务还没有master，当前实例就是master。
* 1.1.8、实例初始化好实例角色后，通过循环模式的定时任务来维护自己的实例角色，定时任务每隔${scheduler-lock.touch-heartbeat-interval-in-milliseconds}执行一次。
* 1.1.9、在循环中，master维持自己的master角色，slave发现不存在master时立刻将自己标记为master。


### 1.2、分布式调度锁
控制一个服务的多个实例的定时任务如何执行。<br>
此功能基于1.1描述的实例角色功能来实现，要与@Scheduler注解一同使用才可以，
每次执行定时任务之前都会根据定时任务方法上标注的@SchedulerLock注解和当前实例的角色来确定是否正真执行该定时任务。

### 1.3、分布式同步锁
实现跨实例的同步方法，在多个实例中，被@SynchronizedLock注解标注的方法，在同一时刻只能有一个实例的一个线程能执行该方法。<br>
在一个应用程序的所有类的所有方法都拥有一个唯一的签名，我们这里将它称为方法签名。

> 此功能的实现流程
* 1.3.1、每次执行被@SynchronizedLock注解标注的方法之前，都尝试将 以当前方法签名为key，以当前实例ID为value 的k-v对（以下将这个k-v对称为同步锁标记）存入redis中，我们将这个尝试操作称为“获取锁”。
* 1.3.2、在存入之前如果发现redis中已经存在了这个方法签名的同步锁标记，则证明已经有其他实例持有当前方法的同步锁。
* 1.3.3、此时使线程Thread.sleep(${synchronized-lock.retry-interval-in-milliseconds})，然后再次尝试获取锁，如此循环往复的获取锁。
* 1.3.4、直到获取锁成功，即成功的把同步锁标记标记存入redis中后，才开始执行被拦截的方法。
* 1.3.5、方法执行完之后，删除redis中的同步锁标记。
* 1.3.6、每一个实例能持有同步锁的最长时间是${synchronized-lock.hold-duration-in-seconds}，以防发生死锁。

## 2、分布式调度锁

### 2.1、属性配置

分布式调度锁有如下属性需要配置，所有属性均有默认值，所以不配置也可以正常使用。

| 属性名 | 必填 | 默认值 | 说明 |
|--------|-----| ------| ---- |
| scheduler-lock.enabled   | 否   |true  |是否启用分布式调度锁功能，默认true，即启用，如不启用则不能使用@SchedulerLock、InstanceRoleService。|
| scheduler-lock.touch-heartbeat-interval-in-milliseconds   | 否   |15000  |维持心跳(实例角色标记)时间间隔，即1.1.8中提到的定时任务每隔多久循环一次。|
| scheduler-lock.heartbeat-expiration-duration-in-seconds   | 否   |45  |心跳(实例角色标记)过期时长，即1.1.4中提到的实例角色标记过期时长。|

### 2.2、实例角色服务示例
此功能是分布式调度锁的基础。
```java
package com.milepost.authenticationExample.schedulerLock.controller;

import com.milepost.core.lock.InstanceRoleService;
import org.apache.commons.lang.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * Created by Ruifu Hua on 2019/12/24.
 */
@Controller
@RequestMapping("/testInstanceRoleServiceController")
public class TestInstanceRoleServiceController {

    /**
     * 注入InstanceRoleService
     */
    @Autowired
    private InstanceRoleService instanceRoleService;

    @ResponseBody
    @GetMapping("/isMaster")
    public Boolean isMaster(@RequestParam(value = "appName", required = false) String appName,
                                       @RequestParam(value = "instanceId", required = false) String instanceId) {
        Boolean isMaster = null;
        if(StringUtils.isNotBlank(appName) && StringUtils.isNotBlank(instanceId)){
            //获取指定服务，指定实例的角色
            isMaster = instanceRoleService.isMaster(appName, instanceId);
        }else{
            //获取当前服务，当前实例的角色
            isMaster = instanceRoleService.isMaster();
        }
        return isMaster;
    }
}
```

### 2.3、分布式调度锁示例
```java
package com.milepost.authenticationExample.schedulerLock.scheduler;

import com.milepost.core.lock.InstanceRoleService;
import com.milepost.core.lock.SchedulerLock;
import com.milepost.core.lock.SchedulerLockModel;
import com.netflix.discovery.EurekaClient;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.List;

/**
 * Created by Ruifu Hua on 2020/3/4.
 */
@Component
public class TestSchedulerLock {

    private static Logger logger = LoggerFactory.getLogger(TestSchedulerLock.class);

	@SchedulerLock(model = SchedulerLockModel.slave)
	@Scheduled(initialDelay = 10000, fixedDelay = 5000)
	public void testSchedulerLockSlave() {
		logger.info("所有slave运行这个定时任务");
	}

    @SchedulerLock(model = SchedulerLockModel.master)
    @Scheduled(initialDelay = 10000, fixedDelay = 10000)
    public void testSchedulerLockMaster() {
        logger.info("只有master运行这个定时任务");
    }
}
```

## 3、分布式同步锁

### 3.1、属性配置

分布式调度锁有如下属性需要配置，所有属性均有默认值，所以不配置也可以正常使用。

| 属性名 | 必填 | 默认值 | 说明 |
|--------|-----| ------| ---- |
| synchronized-lock.enabled   | 否   |true  |是否启用分布式同步锁功能，默认true，即启用，如不启用则不能使用@SynchronizedLock。|
| synchronized-lock.retry-interval-in-milliseconds   | 否   |1000  |分布式同步锁重试获取锁的间隔时间，即1.3.3中提到的线程sleep时长。|
| synchronized-lock.hold-duration-in-seconds   | 否   |45  |每一个实例能占有锁的最长时间，即1.3.6中提到的最长时间。|

### 3.2、分布式同步锁示例
```java
package com.milepost.authenticationExample.SynchronizedLock.service;

import com.milepost.core.lock.SynchronizedLock;
import org.apache.commons.lang.time.DateFormatUtils;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.Date;

/**
 * Created by Ruifu Hua on 2019/12/24.
 */
@Service
public class TestSynchronizedLockService {

    /**
     * 实现跨实例的同步方法，在多个实例中，被@SynchronizedLock注解标注的方法，在同一时刻只能有一个实例的一个线程能执行该方法。
     * @param flag
     * @param sleep
     * @return
     * @throws InterruptedException
     */
    @Transactional
    @SynchronizedLock
    public String testSynchronizedLock(String flag, Integer sleep) throws InterruptedException {
        System.out.println("TestSynchronizedLockService.test1--1" + ", flag="+flag);
        Thread.sleep(sleep*1L);
        System.out.println("TestSynchronizedLockService.test1--2" + ", flag="+flag);
        return DateFormatUtils.ISO_DATETIME_FORMAT.format(new Date());
    }
}
```


