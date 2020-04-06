# 配置文件加密

> 本文档描述如何使用框架的配置文件加密功能。

> 本框架基于com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.1实现配置文件加密。

* UI类和服和Service类服务都可以配置文件加密功能。

请求任何一个Service类应用的接口可以获取文本的密文
```html
curl http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/enc?value=123456
```
返回结果：
```html
ENC(oV1vvxqtTJHlZiwivLOAbPNRyZUb4bBv)
```
把返回的结果配置在yml中即可，如：
```properties
spring.datasource.druid.password=ENC(oV1vvxqtTJHlZiwivLOAbPNRyZUb4bBv)
```
