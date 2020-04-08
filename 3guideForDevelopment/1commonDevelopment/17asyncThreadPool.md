# 异步线程池

> 本文档介绍在框架中如何使用SpringBoot的异步线程池功能。

* 框架开启了异步功能并配置了线程池，线程池参数配置如下：

| 参数名                      | 必填 | 默认值 | 说明|
| ----------------------------|-----|-------|--------|
|thread-pool.core-pool-size|否 |5|线程池核心(最小)线程数|
|thread-pool.max-pool-size|否 |200|线程池最大线程数|
|thread-pool.queue-capacity|否 |10|线程队列最大线程数|
|thread-pool.keep-alive-seconds|否 |60|线程所允许的空闲时间|

* 异步功能使用方法如下：

```java
@GetMapping("/test1")
//在方法上标注@Async注解即可实现异步
@Async
@ResponseBody
public void test1(@RequestParam("param") int param){
    System.out.println("当前线程名称：" + Thread.currentThread().getName());
    //打印线程池信息
    ThreadPoolTaskExecutorConfig.printThreadPoolInfo();
}
```
