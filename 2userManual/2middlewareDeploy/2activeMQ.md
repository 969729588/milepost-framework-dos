# ActiveMQ

> 本文档讲述ActiveMQ 5.8.0 在CentOS7.7 下的安装。

## 1、下载安装包
```bash
[root@localhost software]# pwd /opt/root/software
[root@localhost software]# wget http://archive.apache.org/dist/activemq/apache-activemq/5.8.0/apache-activemq-5.8.0-bin.tar.gz
```

## 2、解压
```bash
[root@localhost software]# tar -zxvf apache-activemq-5.8.0-bin.tar.gz -C /opt/root/module/
```

## 3、启动
```bash
[root@localhost apache-activemq-5.8.0]# /opt/root/module/apache-activemq-5.8.0/bin/activemq start
```

## 4、测试
```bash
[root@localhost milepost]# netstat -an | grep 8161
tcp6       0      0 :::8161                 :::*                    LISTEN     
[root@localhost milepost]# netstat -an | grep 61616
tcp6       0      0 :::61616                :::*                    LISTEN
```

## 5、访问管理页面
地址是：http://${服务器ip}:8161/
用户名：admin；密码：admin

## 6、ActiveMQ配置文件
ActiveMQ配置文件的配置文件在
```bash
/opt/root/module/apache-activemq-5.8.0/conf
```
目录下。