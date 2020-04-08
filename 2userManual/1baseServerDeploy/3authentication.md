# 认证服务部署

> 本文档介绍认证服务的部署。

> 认证服务为整个微服务系统提供单点登录功能，是微服务系统不可缺少的组成部分。

## 1、认证Service部署

## 1.1、软件环境
* JDK1.8+
* Oracle或MySQL
* 已经启动了[EurekaServer](1eurekaServer.md)
* 已经启动了[JWT服务](2jwtServer.md)

## 1.2、所需资料

| 文件名                     | 说明       |
| -------------------------- | ---------- |
| authentication-service-1.0.0.100.jar | 程序jar包 |
| run.sh                     | 启停脚本   |

## 1.3、服务启停

* 启动

```bash
run.sh start
```

* 查看

```bash
run.sh status
```

* 停止

```bash
run.sh stop
```

* 重启

```bash
run.sh restart
```

## 1.4、启动脚本中的参数
* Java参数

| 参数名 | 必填 | 默认值 | 说明 |
| -------| ----| ------| ---- |
| ssl   | 否   |false  |是否用https，true启用，false不启用|


* 命令参数

| 参数名                      | 必填 | 默认值 | 说明                                                         |
| ---------------------------| ---- | ------ | ------------------------------------------------------------ |
|spring.profiles.active|是|  |配置文件环境，<br>dev：开发环境；test：测试环境；prod：生产环境|
|server.port|是| |服务端口，建议设置为9991|
|eureka.client.service-url.defaultZone|是|   |EurekaServer地址|
|eureka.instance.ip-address|是|  |服务绑定ip，配置为服务器ip即可|
|spring.datasource.platform|是| |数据库类型，mysql、oracle|
|spring.datasource.driver-class-name|是|   |数据库驱动类，<br>com.mysql.cj.jdbc.Driver、oracle.jdbc.driver.OracleDriver|
|spring.datasource.url|是| |连接数据库url|
|spring.datasource.username|是 |   |用户名|
|spring.datasource.password|是|    |密码|
|spring.flyway.enabled|是|    |是否开启flyway，此处要配置为false。|
|swagger.title|否 |${spring.application.name} + " swagger"|swagger-ui页面上的title|
|swagger.description|否  |${spring.application.name} + " swagger"|swagger-ui页面上title下方的描述信息|
|spring.redis.host|    是|   |    redis服务ip|
|spring.activemq.broker-url|    否|   |    activemq服务连接url|
|spring.rabbitmq.host|    否| localhost|    rabbitmq服务ip|
|spring.rabbitmq.port|    否| 5672|    rabbitmq服务端口|
|spring.rabbitmq.username|    否|    guest|    rabbitmq服务用户|
|spring.rabbitmq.password|    否|    guest|    rabbitmq服务密码|
|multiple-tenant.tenant|否|default|实例租户，不区分大小写，不支持逗号分割。|
|multiple-tenant.weight|否|1|实例权重，0和正整数。|
|multiple-tenant.label-and|否|   |与标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|multiple-tenant.label-or|否|    |或标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|thread-pool.core-pool-size|否 |5|线程池核心(最小)线程数|
|thread-pool.max-pool-size|否 |200|线程池最大线程数|
|thread-pool.queue-capacity|否 |10|线程队列最大线程数|
|thread-pool.keep-alive-seconds|否 |60|线程所允许的空闲时间|
|scheduler-lock.enabled   | 否   |true  |是否启用分布式调度锁功能，true启用，false不启用，如不启用则不能使用@SchedulerLock、InstanceRoleService。|
|scheduler-lock.touch-heartbeat-interval-in-milliseconds   | 否   |15000  |维持心跳(实例角色标记)时间间隔，即1.1.8中提到的定时任务每隔多久循环一次。|
|scheduler-lock.heartbeat-expiration-duration-in-seconds   | 否   |45  |心跳(实例角色标记)过期时长，即1.1.4中提到的实例角色标记过期时长。|
|synchronized-lock.enabled   | 否   |true  |是否启用分布式同步锁功能，默认true，即启用，如不启用则不能使用@SynchronizedLock。|
|synchronized-lock.retry-interval-in-milliseconds   | 否   |1000  |分布式同步锁重试获取锁的间隔时间，即1.3.3中提到的线程sleep时长。|
|synchronized-lock.hold-duration-in-seconds   | 否   |45  |每一个实例能占有锁的最长时间，即1.3.6中提到的最长时间。|
|tx-lcn.client.manager-address|是|    |分布式事务管理端地址|
|feign.hystrix.enabled|    否|    true|    true开启，false关闭，开启后才能使用@FeignClient注解的fallback属性和fallbackFactory属性|
|ribbon.ConnectTimeout|    否|    8000|    连接超时时间，单位ms，默认8000|
|ribbon.ReadTimeout|    否|    8000|    读取超时时间，单位ms，默认8000|
|hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds|    否|    10000|    hystrix线程执行超时时间，单位ms，默认10000，大于ribbon的超时时间，所以在服务消费者中ribbon先超时。|
|track.enabled|    否|    true|    链路跟踪功能开关，true打开，false关闭|
|track.sampling|    否|    0.1|     链路跟踪功能采样率|


* 表格中关于spring.flyway.*的配置，见[Flyway](../../3guideForDevelopment/1commonDevelopment/2flyway.md)
* 表格中关于swagger.*的配置，见[Swagger](../../3guideForDevelopment/1commonDevelopment/6swagger.md)
* 表格中关于spring.redis.host的配置，见[基于Redis的集中式缓存](../../3guideForDevelopment/1commonDevelopment/15redis.md)
* 表格中关于spring.activemq.broker-url的配置，见[ActiveMQ](../../3guideForDevelopment/1commonDevelopment/13activeMQ.md)
* 表格中关于spring.rabbitmq.*的配置，见[RabbitMQ](../../3guideForDevelopment/1commonDevelopment/14rabbitMQ.md)
* 表格中关于multiple-tenant.*的配置，见[租户、标签、权重](../../3guideForDevelopment/2distributedDevelopment/2tenant.md)
* 表格中关于thread-pool.*的配置，见[异步线程池](../../3guideForDevelopment/1commonDevelopment/17asyncThreadPool.md)
* 表格中关于scheduler-lock.*和synchronized-lock.*的配置，见[分布式锁](../../3guideForDevelopment/2distributedDevelopment/3lock.md)
* 表格中关于tx-lcn.client.manager-address的配置，见[分布式事务集成手册](../../3guideForDevelopment/2distributedDevelopment/1tx-lcn-client.md)
* 表格中关于feign.hystrix.enabled的配置，见[OpenFeign参数配置](../../3guideForDevelopment/2distributedDevelopment/5openFeignConf.md)
* 表格中关于ribbon.*的配置，见[Ribbon参数配置](../../3guideForDevelopment/2distributedDevelopment/6ribbonConf.md)
* 表格中关于hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds的配置，见[Hystrix 熔断和降级](../../3guideForDevelopment/2distributedDevelopment/7hystrix.md)
* 表格中关于track.*的配置，见[SpringCloud Sleuth](../../3guideForDevelopment/2distributedDevelopment/12springCloudSleuth.md)


## 1.5、验证


* 查看日志

服务启动成功后，会在jar包所在的目录下生成logs文件夹，里面存放着日志文件，使用下面的命令查看日志。
```bash
tail -f logs/authentication-service.log -n 300
```
日志中有
```html
...[INFO ] [com.milepost.core.MilepostApplication             : 844 ] - 服务启动完毕。
```
字样表示服务启动成功。


## 1.6、Docker支持

TBD


## 2、认证UI部署

## 2.1、软件环境
* JDK1.8+
* 已经启动了[EurekaServer](1eurekaServer.md)
* 已经启动了[JWT服务](2jwtServer.md)
* 已经启动了认证Service服务

## 2.2、所需资料

| 文件名                     | 说明       |
| -------------------------- | ---------- |
| authentication-ui-1.0.0.100.jar | 程序jar包 |
| run.sh                     | 启停脚本   |

## 2.3、服务启停

* 启动

```bash
run.sh start
```

* 查看

```bash
run.sh status
```

* 停止

```bash
run.sh stop
```

* 重启

```bash
run.sh restart
```

## 2.4、启动脚本中的参数
* Java参数

| 参数名 | 必填 | 默认值 | 说明 |
| -------| ----| ------| ---- |
| ssl   | 否   |false  |是否用https，true启用，false不启用|


* 命令参数

| 参数名                      | 必填 | 默认值 | 说明                                                         |
| ---------------------------| ---- | ------ | ------------------------------------------------------------ |
|spring.profiles.active|是|  |配置文件环境，<br>dev：开发环境；test：测试环境；prod：生产环境|
|server.port|是| |服务端口，建议设置为9991|
|eureka.client.service-url.defaultZone|是|   |EurekaServer地址|
|eureka.instance.ip-address|是|  |服务绑定ip，配置为服务器ip即可|
|spring.redis.host|    是|   |    redis服务ip|
|spring.activemq.broker-url|    否|   |    activemq服务连接url|
|spring.rabbitmq.host|    否| localhost|    rabbitmq服务ip|
|spring.rabbitmq.port|    否| 5672|    rabbitmq服务端口|
|spring.rabbitmq.username|    否|    guest|    rabbitmq服务用户|
|spring.rabbitmq.password|    否|    guest|    rabbitmq服务密码|
|multiple-tenant.tenant|否|default|实例租户，不区分大小写，不支持逗号分割。|
|multiple-tenant.weight|否|1|实例权重，0和正整数。|
|multiple-tenant.label-and|否|   |与标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|multiple-tenant.label-or|否|    |或标签，格式：aa,bb,cc，多个标签支持逗号分割。|
|thread-pool.core-pool-size|否 |5|线程池核心(最小)线程数|
|thread-pool.max-pool-size|否 |200|线程池最大线程数|
|thread-pool.queue-capacity|否 |10|线程队列最大线程数|
|thread-pool.keep-alive-seconds|否 |60|线程所允许的空闲时间|
|scheduler-lock.enabled   | 否   |true  |是否启用分布式调度锁功能，true启用，false不启用，如不启用则不能使用@SchedulerLock、InstanceRoleService。|
|scheduler-lock.touch-heartbeat-interval-in-milliseconds   | 否   |15000  |维持心跳(实例角色标记)时间间隔，即1.1.8中提到的定时任务每隔多久循环一次。|
|scheduler-lock.heartbeat-expiration-duration-in-seconds   | 否   |45  |心跳(实例角色标记)过期时长，即1.1.4中提到的实例角色标记过期时长。|
|synchronized-lock.enabled   | 否   |true  |是否启用分布式同步锁功能，默认true，即启用，如不启用则不能使用@SynchronizedLock。|
|synchronized-lock.retry-interval-in-milliseconds   | 否   |1000  |分布式同步锁重试获取锁的间隔时间，即1.3.3中提到的线程sleep时长。|
|synchronized-lock.hold-duration-in-seconds   | 否   |45  |每一个实例能占有锁的最长时间，即1.3.6中提到的最长时间。|
|feign.hystrix.enabled|    否|    true|    true开启，false关闭，开启后才能使用@FeignClient注解的fallback属性和fallbackFactory属性|
|ribbon.ConnectTimeout|    否|    8000|    连接超时时间，单位ms，默认8000|
|ribbon.ReadTimeout|    否|    8000|    读取超时时间，单位ms，默认8000|
|hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds|    否|    10000|    hystrix线程执行超时时间，单位ms，默认10000，大于ribbon的超时时间，所以在服务消费者中ribbon先超时。|
|track.enabled|    否|    true|    链路跟踪功能开关，true打开，false关闭|
|track.sampling|    否|    0.1|     链路跟踪功能采样率|


* 表格中关于spring.redis.host的配置，见[基于Redis的集中式缓存](../../3guideForDevelopment/1commonDevelopment/15redis.md)
* 表格中关于spring.activemq.broker-url的配置，见[ActiveMQ](../../3guideForDevelopment/1commonDevelopment/13activeMQ.md)
* 表格中关于spring.rabbitmq.*的配置，见[RabbitMQ](../../3guideForDevelopment/1commonDevelopment/14rabbitMQ.md)
* 表格中关于multiple-tenant.*的配置，见[租户、标签、权重](../../3guideForDevelopment/2distributedDevelopment/2tenant.md)
* 表格中关于thread-pool.*的配置，见[异步线程池](../../3guideForDevelopment/1commonDevelopment/17asyncThreadPool.md)
* 表格中关于scheduler-lock.*和synchronized-lock.*的配置，见[分布式锁](../../3guideForDevelopment/2distributedDevelopment/3lock.md)
* 表格中关于feign.hystrix.enabled的配置，见[OpenFeign参数配置](../../3guideForDevelopment/2distributedDevelopment/5openFeignConf.md)
* 表格中关于ribbon.*的配置，见[Ribbon参数配置](../../3guideForDevelopment/2distributedDevelopment/6ribbonConf.md)
* 表格中关于hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds的配置，见[Hystrix 熔断和降级](../../3guideForDevelopment/2distributedDevelopment/7hystrix.md)
* 表格中关于track.*的配置，见[SpringCloud Sleuth](../../3guideForDevelopment/2distributedDevelopment/12springCloudSleuth.md)

## 2.5、验证

* 查看日志

服务启动成功后，会在jar包所在的目录下生成logs文件夹，里面存放着日志文件，使用下面的命令查看日志。
```bash
tail -f logs/authentication-ui.log -n 300
```
日志中有
```html
...[INFO ] [com.milepost.core.MilepostApplication             : 844 ] - 服务启动完毕。
```
字样表示服务启动成功。


## 2.6、登录认证服务
访问http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/login。<br>
初始用户名admin，初始密码admin。

## 2.7、Docker支持

TBD


