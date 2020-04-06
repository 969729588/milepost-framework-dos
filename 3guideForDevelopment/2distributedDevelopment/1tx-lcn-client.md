# 分布式事务客户端集成手册

> 本文档主要介绍如何将分布式事务客户端(下文将分布式事务客户端简称为TC)的集成到项目中。

> 本框架使用5.0.2.RELEASE版本的[TX-LCN](https://www.txlcn.org/zh-cn/)作为分布式事务解决方案， 
本文档所有资料均来自[TX-LCN官方文档](https://www.txlcn.org/zh-cn/docs/preface.html)和[TX-LCN github](https://github.com/codingapi/tx-lcn)。

* 要使用分布式事务功能，需要先部署分布式事务管理端，部署文档见[分布式事务管理端部署](../../2userManual/1baseServerDeploy/3tx-lcn-manager.md)。
* 本框架不需要在UI类服务中集成TC，只需要在Service类服务中集成TC，因为UI类服务不连接数据库，没有datasource。
* 框架中的JWT服务也没有集成TC，因为JWT发放JWT时只是查询数据，不会更改数据。

## 1、配置分布式事务管理端地址
```yaml
tx-lcn:
  client:
    manager-address: 192.168.223.129:8070
```
其他配置见[官方文档](https://www.txlcn.org/zh-cn/docs/setting/client.html)。

## 2、在启动类上增加开启分布式事务功能的注解
```java
package com.milepost.txClientServiceA;

import com.codingapi.txlcn.tc.config.EnableDistributedTransaction;
import com.milepost.service.MilepostServiceApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.scheduling.annotation.EnableScheduling;

@EnableScheduling
@EnableEurekaClient
@EnableFeignClients
@SpringBootApplication
@EnableAspectJAutoProxy
//开启分布式事务的注解
@EnableDistributedTransaction
public class TxClientServiceAApplication extends MilepostServiceApplication {

	public static void main(String[] args) {
		run(TxClientServiceAApplication.class, args);
	}
}
```

## 3、验证
启动后，日志中打印出如下字样证明集成成功
```html
Finally, determined dtx time is 8000ms, tm rpc timeout is 1000 ms, machineId is 53
Searching for more TM...
TC[example-service:9981] established TM cluster successfully!
TM cluster's size: 1
```

## 3、注意事项
增加了@EnableDistributedTransaction注解后必须要配置分布式事务管理端地址，并且保证分布式事务管理端可用，否则启动会报错。
