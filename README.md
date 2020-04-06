# milepost-framework

> 本文档包含milepost-framework框架的所有资料，阅读本文档需要了解微服务、 
分部署、集群、SpringCloud、SpringBoot、相关知识。
阅读时需要先读完概述章节，然后可以按需独立阅读。

# 1、概述
1.1、[术语表](1summary/1term.md)<br>
1.2、[框架简介](1summary/2introduction.md)<br>
1.3、[FQA](1summary/3fqa.md)<br>

# 2、用户使用手册
# 2.1、基础服务部署
2.1.1、[EurekaServer部署](2userManual/1baseServerDeploy/1eurekaServer.md)<br>
2.1.2、[JWT服务部署](2userManual/1baseServerDeploy/2jwtServer.md)<br>
2.1.3、[分布式事务管理端部署](2userManual/1baseServerDeploy/3tx-lcn-manager.md)<br>
2.1.4、[Hystrix Turbine监控服务部署](2userManual/1baseServerDeploy/4milepost-turbine.md)<br>
2.1.5、[SpringBoot Admin监控服务部署](2userManual/1baseServerDeploy/5milepost-admin.md)<br>
2.1.6、[Zipkin服务部署](2userManual/1baseServerDeploy/6zipkin.md)<br>

# 2.2、中间件部署
2.2.1、[Redis](2userManual/2middlewareDeploy/1redis.md)<br>
2.2.2、[ActiveMQ](2userManual/2middlewareDeploy/2activeMQ.md)<br>
2.2.3、[RabbitMQ](2userManual/2middlewareDeploy/3rabbitMQ.md)<br>
2.2.4、[Elasticsearch](2userManual/2middlewareDeploy/4elasticsearch.md)<br>
2.2.5、[Kibana](2userManual/2middlewareDeploy/5kibana.md)<br>


# 3、开发指南
## 3.1、常规开发
3.1.1、[==使用本框架构建一个项目并打包发布到Linux上](3guideForDevelopment/1commonDevelopment/1projectExample.md)<br>
3.1.2、[框架日志](3guideForDevelopment/1commonDevelopment/2logger.md)<br>
3.1.3、[Flyway](3guideForDevelopment/1commonDevelopment/3flyway.md)<br>
3.1.4、[发送邮件](3guideForDevelopment/1commonDevelopment/4senderMail.md)<br>
3.1.5、[Mybatis缓存](3guideForDevelopment/1commonDevelopment/5mybatisCache.md)<br>
3.1.6、[Mybatis Generator](3guideForDevelopment/1commonDevelopment/6mybatisGenerator.md)<br>
3.1.7、[Swagger](3guideForDevelopment/1commonDevelopment/7swagger.md)<br>
3.1.8、[获取有效的token](3guideForDevelopment/1commonDevelopment/8getToken.md)<br>
3.1.9、[请求被oauth保护的接口并在接口中获取用户信息](3guideForDevelopment/1commonDevelopment/9auth.md)<br>
3.1.10、[RestTemplate](3guideForDevelopment/1commonDevelopment/10restTemplate.md)<br>
3.1.11、[单元测试](3guideForDevelopment/1commonDevelopment/11junitTest.md)<br>
3.1.12、[配置文件加密](3guideForDevelopment/1commonDevelopment/12configEncryption.md)<br>
3.1.13、[多数据源](3guideForDevelopment/1commonDevelopment/13dynamicDs.md)<br>
3.1.14、[ActiveMQ](3guideForDevelopment/1commonDevelopment/14activeMQ.md)<br>
3.1.15、[RabbitMQ](3guideForDevelopment/1commonDevelopment/15rabbitMQ.md)<br>
3.1.16、[基于Redis的集中式缓存](3guideForDevelopment/1commonDevelopment/16redis.md)<br>
3.1.17、[优雅停止服务](3guideForDevelopment/1commonDevelopment/17gracefullyStop.md)<br>
3.1.18、[异步线程池](3guideForDevelopment/1commonDevelopment/18asyncThreadPool.md)<br>
3.1.19、[自定义异常页面](3guideForDevelopment/1commonDevelopment/19customExPage.md)<br>
3.1.20、[文件上传](3guideForDevelopment/1commonDevelopment/20fileupload.md)<br>
3.1.21、[定制Tomcat](3guideForDevelopment/1commonDevelopment/21customizerTomcat.md)<br>


## 3.2、分布式开发
3.2.1、[分布式事务集成手册](3guideForDevelopment/2distributedDevelopment/1tx-lcn-client.md)<br>
3.2.2、[租户、标签、权重](3guideForDevelopment/2distributedDevelopment/2tenant.md)<br>
3.2.3、[分布式锁](3guideForDevelopment/2distributedDevelopment/3lock.md)<br>
3.2.4、[OpenFeign参数传递](3guideForDevelopment/2distributedDevelopment/4openFeignParam.md)<br>
3.2.5、[OpenFeign参数配置](3guideForDevelopment/2distributedDevelopment/5openFeignConf.md)<br>
3.2.6、[Ribbon参数配置](3guideForDevelopment/2distributedDevelopment/6ribbonConf.md)<br>
3.2.7、[Hystrix 熔断和降级](3guideForDevelopment/2distributedDevelopment/7hystrix.md)<br>
3.2.8、[Hystrix Dashboard](3guideForDevelopment/2distributedDevelopment/8hystrixDashboard.md)<br>
3.2.9、[Hystrix Turbine](3guideForDevelopment/2distributedDevelopment/9hystrixTurbine.md)<br>
3.2.10、[SpringBoot Admin](3guideForDevelopment/2distributedDevelopment/10springbootAdmin.md)<br>
3.2.11、[Spring Cloud Bus](3guideForDevelopment/2distributedDevelopment/11springCloudBus.md)<br>
3.2.12、[SpringCloud Sleuth](3guideForDevelopment/2distributedDevelopment/12springCloudSleuth.md)<br>


# 4、其他
## --4.1、框架测试

<!--
# 5、框架参考
* 《深入理解Spring Cloud与微服务构建 第2版》
* 方志朋相关书籍、博客
* [Spring Boot 2.1.0 Reference Guid](https://docs.spring.io/spring-boot/docs/2.1.0.RELEASE/reference/html/)<br>
* [Spring Cloud Greenwich.SR5 Reference Guid](https://cloud.spring.io/spring-cloud-static/Greenwich.SR5/)<br>
-->

<!--
1、5年以上软件架构设计工作经验，精通微服务架构设计者优先；
2、5年以上软件开发相关背景（Java， Python 方向）；
3、3年以上 DevOps 工作经验；
4、熟悉Docker、Kubernetes、Gitlab、Jenkins、Nginx、Redis、MySQL、Ansible、Zabbix、消息队列等相关工具及软件；
5、了解敏捷开发方法论；
6、良好的英文沟通能力；
-->

<!--
需要补充的文档：
License
首页那个大图片需要更改一下，

-->

<!--
需要研究的：
1、配置中心，动态刷新配置。
2、安全相关
3、docker，k8s，自动化部署，微服务管控，
4、利用actuator的env和refresh端点实现动态修改多租户相关配置
5、flyway的企业版才能支持oracle11G
使用springboot自己的数据库初始化工具
https://docs.spring.io/spring-boot/docs/2.1.10.RELEASE/reference/html/howto-database-initialization.html#howto-initialize-a-database-using-spring-jdbc
-->

