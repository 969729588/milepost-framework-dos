# 分布式事务服务端部署

> 本框架使用1.0.0版本的[seata](https://seata.io/zh-cn/index.html)作为分布式事务解决方案， 
本文档所有资料均来自[seata官方文档](https://seata.io/zh-cn/docs/overview/what-is-seata.html)。


## 1、软件环境

* JDK1.8+
* Oracle或MySQL
* 已经启动了EurekaServer

## 2、获取1.0.0版本的seata服务端jar包

###2.1、下载源码编译打包

github源码下载地址：https://github.com/seata/seata ， 下载1.0.0版本的源码。

项目导入到idea中报错，需要install一下。

编译打包：

```html
1. 删除seata-1.0.0\distribution目录下的bin、conf、lib目录。
2. 进入源码包根目录seata-1.0.0，执行mvn clean install -DskipTests=true -P release-seata。
3. 在seata-1.0.0\distribution目录下的target目录下解压相应的压缩包即可。
```
如编译打包报错，则可以下载官方编译好的jar包。

###2.2、下载官方编译好的jar包

github jar包下载地址：https://github.com/seata/seata/releases ，下载1.0.0版本的jar包。

至此得seata-server-1.0.0.zip，解压后如下：

```html
seata
├── bin
│   ├── seata-server.bat
│   └── seata-server.sh
├── conf
│   ├── file.conf
│   ├── file.conf.example
│   ├── .file.conf.swo
│   ├── .file.conf.swp
│   ├── logback.xml
│   ├── META-INF
│   │   └── services
│   │       ├── io.seata.core.lock.Locker
│   │       ├── io.seata.core.store.db.DataSourceGenerator
│   │       ├── io.seata.server.session.SessionManager
│   │       └── io.seata.server.store.TransactionStoreManager
│   ├── README.md
│   ├── README-zh.md
│   └── registry.conf
└── lib
    ├── animal-sniffer-annotations-1.17.jar
    ├── antlr-2.7.7.jar
    ├── antlr-runtime-3.4.jar
    ├── aopalliance-1.0.jar
    ├── ...
```

## 3、修改配置文件

### 3.1、seata/conf/registry.conf
```html
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "eureka"
  eureka {
    # eureka地址
    serviceUrl = "http://ip:port/eureka"
    # 注册到EurekaServer中的应用名称，必须与客户端保持一致，
    # 客户端默认使用“tx-server”作为服务端名称，因此需要配置为“tx-server”。
    application = "tx-server"
    # 多个应用集群时候，每个应用的权重值
    weight = "1"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "file"
  
  file {
    name = "file.conf"
  }
}
```

### 3.2、seata/conf/file.conf

```html
## 事务日志存储
store {
  ## 存储模式，file、db
  mode = "db"

  ## 数据库
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
    datasource = "dbcp"
    ## mysql/oracle/h2/oceanbase etc.
    db-type = ""
    ## 驱动类
    driver-class-name = ""
    ##url
    url = ""
    ## 用户名
    user = ""
    ## 密码
    password = ""
  }
}
```

### 3.3、seata/conf/logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <!--更改日志文件存储目录为当前目录，在当前目录下生成日志文件-->
    <property name="LOG_HOME" value="./logs/seata"/>

    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%date{yyyy-MM-dd HH:mm:ss.SSS} %-5level[%thread]%logger{56}.%method:%L -%msg%n</Pattern>
        </encoder>
    </appender>

    <appender name="seata-default"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_HOME}/seata-server.log</file>
        <append>true</append>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/seata-server.log.%d{yyyy-MM-dd}.%i</fileNamePattern>
            <maxFileSize>2GB</maxFileSize>
            <MaxHistory>7</MaxHistory>
            <totalSizeCap>7GB</totalSizeCap>
            <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder>
            <Pattern>%date %level %msg%n%n</Pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <logger name="io.seata.server.store.file.FileTransactionStoreManager" additivity="false">
        <level value="INFO"/>
        <appender-ref ref="seata-default"/>
    </logger>

    <root level="INFO">
        <appender-ref ref="seata-default"/>
        <appender-ref ref="stdout"/>
    </root>
</configuration>
```


## 4、创建数据库表

建表脚本在源码包的seata-1.0.0\script\server\db目录下，支持mysql、oracle、postgresql，mysql建表脚本如下
```sql
-- the table to store GlobalSession data
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME,
    `gmt_modified`      DATETIME,
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(96),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

```

## 5、服务启停

* 进入seata目录下，给启动脚本授权
```bash
chmod +x bin/seata-server.sh
```

* 启动

```bash
nohup ./bin/seata-server.sh -h 注册到EurekaServer的ip &
```

* 停止

杀掉进程即可

##  4、启动脚本中的参数

* 命令参数

| 参数名                      | 必填 | 默认值 | 说明                                                         |
| ---------------------------| ---- | ------ | ------------------------------------------------------------ |
|-h|是|  |注册到EurekaServer的ip|
|-p|否|8091|Server rpc 监听端口|
|-n|否|  |Server node，多个Server时，需区分各自节点，<br>用于生成不同区间的transactionId，以免冲突|

## 5、验证

* 查看日志

服务启动成功后，会在seata目录下生成logs文件夹，里面存放着日志文件，使用下面的命令查看日志。
```bash
tail -f logs/seata/seata-server.log -n 300
```

## 6、Docker支持

TBD
