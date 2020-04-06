# 框架日志

> 本文档介绍框架的系统模块。

> SpringBoot默认使用SLF4j和logback作为日志框架，本框架并没有改变SpringBoot默认的选择， 
而是在此基础上增加了默认的日志打印行为，开发者也可以自定义自己的日志打印行为。

在继续阅读下面的文档之前，需要知道本框架的spring.profiles.active参数有三个值，分别是dev(开发环境)、test(测试环境)、prod(生产环境)。

## 1、日志打印方式
当spring.profiles.active=dev(默认值是dev)时，框架将日志打印到控制台。<br>
当spring.profiles.active=test或者prod时，框架将日志打印到文件。

## 2、日志打印级别
三个环境的日志打印级别如下：
```xml
<logger name="com.milepost" level="debug"/>
<root level="info">
    <appender-ref ref="console" />
</root>
```

## 3、日志文件位置和滚动规则
```xml
<appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>./logs/${spring.application.name}.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>./logs/${spring.application.name}-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
        <MaxHistory>365</MaxHistory>
        <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>5MB</maxFileSize>
        </timeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>
</appender>
```

## 4、改变框架的默认日志配置
只需要在类路径下增加名logback-spring.xml文件来覆盖框架默认的日志配置，下面是框架默认的日志配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<springProperty name="spring.application.name" source="spring.application.name"/>
	<springProperty name="eureka.instance.instance-id" source="eureka.instance.instance-id"/>
	<include resource="org/springframework/boot/logging/logback/defaults.xml" />
	<property name="LOG_FILE" value="./logs/${spring.application.name}.log"/>
	<!--开发环境-->
	<springProfile name="dev">
		<property name="LOG_PATTERN" value="[${eureka.instance.instance-id}] %yellow(%d{yyyy-MM-dd HH:mm:ss.SSS}) - [%highlight(%-5level)] [%green(%-50logger{50}): %blue(%-4line)] - %msg%n"/>
		<include resource="logback-dev.xml"/>
	</springProfile>
	<!--测试环境-->
	<springProfile name="test">
		<property name="LOG_PATTERN" value="[${eureka.instance.instance-id}] %d{yyyy-MM-dd HH:mm:ss.SSS} - [%-5level] [%-50logger{50}: %-4line] - %msg%n"/>
		<include resource="logback-tes.xml"/>
	</springProfile>
	<!--生产环境-->
	<springProfile name="prod">
		<property name="LOG_PATTERN" value="[${eureka.instance.instance-id}] %d{yyyy-MM-dd HH:mm:ss.SSS} - [%-5level] [%-50logger{50}: %-4line] - %msg%n"/>
		<include resource="logback-prod.xml"/>
	</springProfile>
</configuration>
```

## 5、通过Restful接口动态修改日志打印级别

* 查看所有包/类的日志级别
```html
curl http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/milepost-actuator/loggers
```

* 查看指定包/类的日志级别
```html
curl http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/milepost-actuator/loggers/${指定包/类全限定名}
```

* 修改指定包/类日志级别(没有数据返回，返回状态码是204。)
```html
curl http(s)://${eureka.instance.ip-address}:${server.port}/${server.servlet.context-path}/milepost-actuator/loggers/${指定包/类全限定名} \
-H "Content-Type:application/json" \
-X POST \
-d '{"configuredLevel":"${日志级别}"}'
```





