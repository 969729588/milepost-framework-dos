# 优雅停止服务

> 本文档如何优雅的停止一个服务并释放掉占用的资源。

一般来讲，使用框架的run.sh脚本来停止服务即可，
```bash
[root@localhost authentication-ui]# ./run.sh stop
kill process...
kill process...
no process
[root@localhost authentication-ui]# 
```

服务的日志中打印如下表示正常停止了服务
```html
xxxx] - ------服务关闭成功------
xxxx] - Unregistering application AUTHENTICATION-UI with eureka with status DOWN
xxxx] - Saw local status change event StatusChangeEvent [timestamp=1584449292461, current=DOWN, previous=UP]
xxxx] - DiscoveryClient_AUTHENTICATION-UI/192.168.223.129:tenant1:authentication-ui:9990: registering service...
xxxx] - Shutting down ExecutorService
xxxx] - 关闭线程池...
xxxx] - DiscoveryClient_AUTHENTICATION-UI/192.168.223.129:tenant1:authentication-ui:9990 - registration status: 204
xxxx] - Shutting down ExecutorService 'taskScheduler'
xxxx] - Shutting down ExecutorService 'applicationTaskExecutor'
xxxx] - Shutting down ExecutorService
xxxx] - 关闭线程池...
xxxx] - Shutting down ExecutorService
xxxx] - 关闭线程池...
xxxx] - Shutting down DiscoveryClient ...
xxxx] - Unregistering ...
xxxx] - DiscoveryClient_AUTHENTICATION-UI/192.168.223.129:tenant1:authentication-ui:9990 - deregister  status: 200
xxxx] - Completed shut down of DiscoveryClient
```
从日志中可以看到，应用关闭了线程池、定时任务、DiscoveryClient，从EurekaServer中取消注册，
这样可以使服务实例立刻从EurekaServer中下线并从服务列表中消失。

服务停止后使用run.sh检测
```bash
[root@localhost authentication-ui]# ./run.sh status
root      3984  3982  0 20:52 pts/3    00:00:00 grep authentication-ui-1.0.0.100.jar
[root@localhost authentication-ui]# 
```
发现没用进程了，表示确实是停止了。

如run.sh无法停止服务推荐使用
```bash
kill -15 pid
```
命令来停止服务，如依然不能成功停止服务器，则说明程序有bug，即不能正常释放资源，不推荐使用
```bash
kill -9 pid
```
来强制停止服务。

