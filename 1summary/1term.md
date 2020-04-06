# 术语表

> 这里介绍本文档中涉及到的所有名次及术语。

* 项目：即工程，指静态的代码。
* 服务：是一个可以对外提供http/https服务的应用程序，如tomcat容器中的一个应用、SpringBoot应用等。
* 微服务：在SpringCloud中将服务称为“微服务”。
* 实例(服务实例)：项目启动后就变成了服务实例，服务实例可以对外提供服务，而项目是静态的代码。
* 集群：同一个(微)服务部署多个实例就称为集群。
* 分布式：将一个完整的系统按照业务功能拆分成多个独立的子系统， 
每个子系统称为一个“微服务”。这些子系统独立部署，它们之间通过Http方式通信。
* UI类服务：继承框架UI的服务称之为“UI类服务”。
* Service类服务：继承框架Service的服务称之为“Service类服务”。
* 框架服务：EurekaServer、JWT服务、认证Service、认证UI，这些服务称为“框架服务”， 
框架提供jar包，部署后可以直接使用。
* 业务系统服务：除EurekaServer、JWT服务、认证Service、认证UI之外的其他服务都称为“业务系统服务”。
* 租户、标签、权重：见[租户、标签、权重](../3guideForDevelopment/2distributedDevelopment/2tenant.md)