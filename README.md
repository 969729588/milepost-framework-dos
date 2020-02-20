
# milepost-framework

milepost-framework 是一套微服务框架，基于SpringCloud的Greenwich.RELEASE版本构建， 
在SpringCloud基础上增加了微服务架构下开发常用的组件：

* 集群部署的多实例应用之间定时任务如何运行 
* 集群部署的多实例应用如何像单实例应用那样实现同步代码块
* 基于seata构建的分布式事务解决方案
* ActiveMQ、RestTemplate、Redis、
* 注册到一个EurekaServer上的服务按照租户隔离
* 多实例应用按照实例标签和权重负载
* 基于OAuth2和JWT实现认证服务，为所有业务系统服务提供统一认证服务
* Mybatis-generator 
* Swagger
* 日志
* Flyaway
* 、、、


使用本框架可以专注于业务应用的构建，减少了很多重复性的工作，提高效率。

本文档列出框架所涉及到的所有文档目录，所有文档均从这里进入。

# 1、基础服务部署

* [EurekaServer部署](baseServerDeploy/eurekaServer.md)
* [JWT服务部署](baseServerDeploy/jwtServer.md)
* [分布式事务服务端部署](baseServerDeploy/seataServer.md)
* [认证服务部署]()










