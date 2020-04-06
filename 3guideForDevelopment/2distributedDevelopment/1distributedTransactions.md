# 分布式事务客户端集成手册

> 本框架使用1.0.0版本的[seata](https://seata.io/zh-cn/index.html)作为分布式事务解决方案， 
本文档所有资料均来自[seata官方文档](https://seata.io/zh-cn/docs/overview/what-is-seata.html)。

* 要使用分布式事务功能，需要先部署分布式事务服务端，部署文档见[分布式事务服务端部署](../../2userManual/1baseServerDeploy/3seataServer.md)。
* 本框架不需要在UI类服务集成分布式客户端，只需要在Service类服务集成分布式客户端。
* 参照Seata官方的集成[demo](https://github.com/seata/seata-samples/tree/master/springcloud-eureka-seata)。

## 1、加入依赖
```yaml
<dependency>
    <groupId>com.milepost</groupId>
    <artifactId>milepost-dist-transaction</artifactId>
</dependency>
```

## 2、创建数据库表
```sql
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

## 3、相关配置
| 参数名                      | 必填 | 默认值 | 说明|
| ----------------------------|-----|-------|--------|
|dist-transaction.enabled|否 |false|是否启用，默认false，不启用，对应seata.enabled|
|dist-transaction.tx-server-app-name|否  |tx-server  |分布式事务服务端注册到EurekaServer中的服务名称|

## 4、注意事项
如不配置dist-transaction.enabled或者将其配置为false，表示不启用分布式事务， 
此时最好不要在pom中加入milepost-dist-transaction依赖，虽然对项目没有影响，但也不推荐这样做。
