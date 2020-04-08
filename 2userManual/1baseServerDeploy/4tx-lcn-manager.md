# 分布式事务管理端部署

> 本文档主要介绍分布式事务管理端(下文将分布式事务管理端简称为TM)的部署。

> 本框架使用5.0.2.RELEASE版本的[TX-LCN](https://www.txlcn.org/zh-cn/)作为分布式事务解决方案， 
本文档所有资料均来自[TX-LCN官方文档](https://www.txlcn.org/zh-cn/docs/preface.html)和[TX-LCN github](https://github.com/codingapi/tx-lcn)。

## 1、软件环境

* JDK1.8+
* Mysql5.6+
* Redis3.2+

## 2、所需资料

| 文件名                     | 说明       |
| -------------------------- | ---------- |
| txlcn-tm-5.0.2.RELEASE.zip | 程序启动包 |
| run.sh                     | 启停脚本   |

启动包加压后如下：
```html
.
└── txlcn-tm-5.0.2.RELEASE
    ├── application-meetzy.properties
    ├── application.properties
    ├── application-second.properties
    ├── application-third.properties
    ├── application-ujued.properties
    ├── lib[1、程序jar的依赖库]
    │   ├── activation-1.1.jar
    │   ├── antlr-2.7.7.jar
    │   ├── aspectjweaver-1.8.13.jar
    │   ├── ...
    │   ├── ...
    ├── static
    │   └── admin
    │       ├── assets
    │       │   └── 8c382430f673ad2237bbf19e5c8a4b00.png
    │       ├── css
    │       │   └── index.css
    │       ├── favicon.png
    │       ├── index.html
    │       └── js
    │           └── index.js
    ├── txlcn-tm-5.0.2.RELEASE.jar[2、程序jar包]   
    ├── txlcn-tm-5.0.2.RELEASE-sources.jar
    └── tx-manager.sql[3、数据库脚本]
```
我们需要的是：<br>
1、程序jar包的依赖库<br>
2、程序jar包<br>
3、数据库脚本<br>

## 3、创建数据库表

用启动包中的[3、数据库脚本]创建数据库表。
```sql
CREATE DATABASE IF NOT EXISTS  `tx-manager` DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
USE `tx-manager`;

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

DROP TABLE IF EXISTS `t_tx_exception`;
CREATE TABLE `t_tx_exception`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `group_id` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `unit_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `mod_id` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `transaction_state` tinyint(4) NULL DEFAULT NULL,
  `registrar` tinyint(4) NULL DEFAULT NULL,
  `ex_state` tinyint(4) NULL DEFAULT NULL COMMENT '0 待处理 1已处理',
  `remark` varchar(10240) NULL DEFAULT NULL COMMENT '备注',
  `create_time` datetime(0) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 967 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

SET FOREIGN_KEY_CHECKS = 1;
```

## 4、服务部署

将[1、程序jar的依赖库]、[2、程序jar包]和启动脚本[run.sh]放到同一个目录下。

## 5、服务启停


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

* 访问后台
http://${tx-lcn.manager.host}:${server.port}/admin/index.html<br>
默认密码是123456

## 6、启动脚本中的参数
这里的参数是常用的，具体见[官方文档](https://www.txlcn.org/zh-cn/docs/setting/manager.html)。

* 命令参数

| 参数名      | 必填 | 默认值 | 说明                                                         |
| ----------| ---- | ------ | ------------------------------------------------------------ |
|server.port|   否|  7970|   TM监听Socket端口Http端口，用来访问事务管理系统后台的。|
|spring.datasource.driver-class-name|   否|com.mysql.jdbc.Driver|数据库驱动|
|spring.datasource.url|   是|    |数据库连接url|
|spring.datasource.username|   是|    |数据库用户名|
|spring.datasource.password|   是|    |数据库密码|
|tx-lcn.manager.host|   是|    |TM监听ip，配置成服务器ip即可|
|tx-lcn.manager.port|   否|  8070|TM监听Socket端口，即TC连接请求端口|
|tx-lcn.manager.admin-key|   否|  123456|TM后台登陆密码|
|spring.redis.host|   是|  |redis服务ip|
|spring.redis.port|   否|  6379|redis服务port|
|tx-lcn.logger.enabled|   是|  |是否开启日志，true/false|
|logging.level.com.codingapi|   否|  debug|日志打印级别|
|tx-lcn.logger.driver-class-name|   否|${spring.datasource.driver-class-name}|日志数据库驱动|
|tx-lcn.logger.url|   否|${spring.datasource.url}|日志数据库连接url|
|tx-lcn.logger.username|   否|${spring.datasource.username}|日志数据库用户名|
|tx-lcn.logger.password|   否|${spring.datasource.password}|日志数据库密码|

## 7、注意事项
分布式事务管理端不需要配置租户、标签、权重等参数，所有实例共用一个分布式事务管理端。

## 8、Docker支持

TBD
