# Mybatis缓存

> 本文档描述框架中对Mybatis缓存的使用。

> 本框架通过Mybatis整合了Ehcache，实现全自动化的缓存，不需要手动编写代码来控制。

## 1、缓存的开启与关闭

框架默认开启Mybatis的缓存机制，如需关闭，只需要将[Mybatis Generator](5mybatisGenerator.md)
生成的XxxxMapper.xml文件中的最后一行
```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache" />
```
注释掉即可关闭当前Mapper的缓存。

## 2、缓存配置

框架默认的缓存配置文件如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
 	<diskStore path="./mybatis-ehcache" />
 	<defaultCache 
   		maxElementsInMemory="10000" 
   		maxElementsOnDisk="10000000"
   		eternal="false" 
   		overflowToDisk="true" 
   		timeToIdleSeconds="120"
   		timeToLiveSeconds="120" 
   		diskExpiryThreadIntervalSeconds="120"
   		memoryStoreEvictionPolicy="LRU">
 	</defaultCache>
</ehcache>
```
各属性说明：
```html
diskStore.path：指定数据在磁盘中的存储位置。
defaultCache：当借助CacheManager.add("demoCache")创建Cache时，EhCache便会采用<defalutCache/>指定的的管理策略。

以下属性是必须的：
defaultCache.maxElementsInMemory：在内存中缓存的element的最大数目。
defaultCache.maxElementsOnDisk：在磁盘上缓存的element的最大数目，若是0表示无穷大。
defaultCache.eternal：设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断。
defaultCache.overflowToDisk：设定当内存缓存溢出的时候是否将过期的element缓存到磁盘上。

以下属性是可选的：
defaultCache.timeToIdleSeconds：设置对象在失效前的允许闲置时间。仅当eternal=false时使用，默认值是0，也就是可闲置时间无穷大。
defaultCache.timeToLiveSeconds：设置对象在失效前允许存活时间。最大时间介于创建时间和失效时间之间。仅当eternal=false时使用，默认是0，也就是对象存活时间无穷大。
defaultCache.diskSpoolBufferSizeMB：这个参数设置DiskStore(磁盘存储)的缓存区大小，默认是30MB，每个Cache都应该有自己的一个缓冲区。
defaultCache.diskPersistent：在JVM重启的时候是否启用磁盘保存EhCache中的数据，默认是false。
defaultCache.diskExpiryThreadIntervalSeconds：磁盘缓存的清理线程运行间隔，默认是120秒，每隔120s，相应的线程会进行一次EhCache中数据的清理工作。
defaultCache.memoryStoreEvictionPolicy：当内存缓存达到最大，有新的element加入的时候， 移除缓存中element的策略。默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出）。
```

可以通过在类路径下放置一个名称为ehcache.xml的文件来覆盖框架的默认配置。

## 3、缓存效果的演示
> 如使用框架默认的缓存配置文件，则整个演示过程需要在120s之内完成。演示中的请求接口可通过Service类服务的[Swagger](6swagger.md)来完成。

* 启动服务，请求一次Service类服务的查询person列表接口，控制台打印如下日志：
```html
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.631578947368421
==>  Preparing: SELECT count(0) FROM person WHERE (FIRST_NAME LIKE ?) 
==> Parameters: %%(String)
<==      Total: 1
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.6
==>  Preparing: select ID, FIRST_NAME, LAST_NAME, BIRTH, SCORE from person WHERE ( FIRST_NAME like ? ) LIMIT ? 
==> Parameters: %%(String), 10(Integer)
<==      Total: 3
```

* 120s之内，再发一次同样的请求，控制台打印如下日志：
```html
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5652173913043478
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5833333333333334
```
> 通过日志发现，第二次请求没有打印查询数据的sql语句，即120s之内连续发送两次(多次)同样的请求，
只有第一次请求真正连接数据库查询数据，其他都是存缓存中取数据。

* 当服务处于从缓存中取数据的状态时，请求一次更改person数据的接口，然后再请求查询person列表的接口，控制台打印如下日志：
```html
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5555555555555556
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5714285714285714
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5862068965517241
--以上日志表明服务处于从缓存中取数据的状态

Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.6
==>  Preparing: update person set FIRST_NAME = ?, LAST_NAME = ?, BIRTH = ?, SCORE = ? where ID = ? 
==> Parameters: zhang-3(String), null, null, null, 1(String)
<==    Updates: 1
--以上日志是请求一次更改person数据接口所打印的

Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5806451612903226
==>  Preparing: SELECT count(0) FROM person WHERE (FIRST_NAME LIKE ?) 
==> Parameters: %%(String)
<==      Total: 1
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5625
==>  Preparing: select ID, FIRST_NAME, LAST_NAME, BIRTH, SCORE from person WHERE ( FIRST_NAME like ? ) LIMIT ? 
==> Parameters: %%(String), 10(Integer)
<==      Total: 3
--以上日志是请求一次更改person数据接口之后再请求一次查询person列表接口所打印的

Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5757575757575758
Cache Hit Ratio [com.milepost.authenticationService.person.dao.PersonMapper]: 0.5882352941176471
--以上日志是连续请求查询person列表接口所打印的
```

> 通过日志发现，当有数据被更改时，缓存将被清除，使查询能获取到最新数据。