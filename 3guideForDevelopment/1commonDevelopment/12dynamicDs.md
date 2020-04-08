# 多数据源

> 本文档描述如何使用框架的多数据源功能。

## 1、在yml中配置多数据源
```yaml
spring:
  datasource:
    # flyway使用这个参数，多数据源不需要配置此配置项，支持oracle, mysql
    platform: mysql
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.223.136:3306/milepost_auth?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=GMT%2B8
    username: root
    password: admin123
    # 连接池属性配置见https://github.com/brettwooldridge/HikariCP，以下几个是常用的
    hikari:
      connection-timeout: 30001
      idle-timeout: 600001
      max-lifetime: 1800001
      maximum-pool-size: 11
      minimum-idle: 11
    one:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://192.168.223.136:3306/milepost_auth_1?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=GMT%2B8
      username: root
      password: admin123
      hikari:
        connection-timeout: 30002
        idle-timeout: 600002
        max-lifetime: 1800002
        maximum-pool-size: 12
        minimum-idle: 12
    two:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://192.168.223.136:3306/milepost_auth_2?useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&serverTimezone=GMT%2B8
      username: root
      password: admin123
```

注意，多数据源one、two中未配置的数据将使用主数据源的配置。

## 2、调用service前设置多数据源key
调用service方法之前，调用下面过的方法设置数据源key，
```java
DataSourceContextHolder.setDataSource("one");
```
在这行代码之后调用的service中的方法使用的就是“one”数据源， 
直到调用下面的方法清空数据源key为止都是使用“one”数据源
```java
DataSourceContextHolder.clearDataSource();
```
如果设置的数据源key不存在，则使用默认(主)数据源。


