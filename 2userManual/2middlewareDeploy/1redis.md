# Redis

> 本文档讲述redis 5.0.7在CentOS7.7下的安装，包括单例、哨兵、集群三种方式的配置。

## 1、安装gcc
```bash
[root@localhost redis-5.0.7]# yum install gcc-c++
[root@localhost redis-5.0.7]# gcc --version
gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-23)
Copyright (C) 2010 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## 2、安装redis

* 下载
```bash
[root@localhost software]# pwd
/opt/root/software
[root@localhost software]# wget http://download.redis.io/releases/redis-5.0.7.tar.gz
```

* 解压

```bash
[root@localhost software]# tar -zvxf redis-5.0.7.tar.gz -C /opt/root/module/
```

* 编译
```bash
[root@localhost redis-5.0.7]# pwd
/opt/root/module/redis-5.0.7
[root@localhost redis-5.0.7]# make

    LINK redis-cli
    CC redis-benchmark.o
    LINK redis-benchmark
    CC redis-check-dump.o
    LINK redis-check-dump
    CC redis-check-aof.o
    LINK redis-check-aof

Hint: It's a good idea to run 'make test' ;)

make[1]: Leaving directory `/opt/root/module/redis-5.0.7/src'
```

* 安装

```bash
[root@localhost redis-5.0.7]# make install
```
> 至此，如果不报错，则redis安装完毕。

## 3、单例模式
### 3.1、 复制配置文件
```bash
[root@localhost redis-5.0.7]# mkdir redis_stand-alone
[root@localhost redis-5.0.7]# cp redis.conf redis_stand-alone/redis_6379.conf
```
### 3.2、修改redis_6379.conf配置文件

用以下属性覆盖配置文件中的属性
```conf
# 后台启动。
daemonize yes

# 否则只能通过本机访问redis。
protected-mode no

# 绑定服务器真实ip。
bind 192.168.223.129

# 进程ID文件。
pidfile /var/run/redis_6379.pid

# 绑定端口。
port 6379

# 日志文件。
logfile log_6379.log

# dump文件名。
dbfilename dump_6379.rdb

# 客户端闲置多少秒后，断开连接。
timeout 300

# 日志级别，支持debug，verbose，notice，warning。
loglevel notice

# 最大客户端链接数。
maxclients 1000

# 如不开启可以不配置。
# requirepass 123456

# 每秒一次同步磁盘。
appendfsync everysec

# 最大内存，单位字节，4294967296字节=4GB。
maxmemory 4294967296

# 关闭日志记录（严格不丢数据的场合应该配置成yes）
appendonly no
```

### 3.3、启动
```bash
[root@localhost redis_stand-alone]# /opt/root/module/redis-5.0.7/src/redis-server /opt/root/module/redis-5.0.7/redis_stand-alone/redis_6379.conf
```

## 3.4、检查
```bash
[root@localhost redis_stand-alone]# ps -ef | grep redis-server
root      5402     1  0 18:43 ?        00:00:00 /opt/root/module/redis-5.0.7/src/redis-server 0.0.0.0:6379                                            
root      5425  4801  0 18:45 pts/2    00:00:00 grep redis-server
[root@localhost redis_stand-alone]# redis-cli -h 192.168.223.129 -p 6379
192.168.223.129:6379> ping
PONG
192.168.223.129:6379> set k1 v1
OK
192.168.223.129:6379> get k1
"v1"
192.168.223.129:6379> quit
[root@localhost redis_stand-alone]# 
```

## 4、哨兵模式

### 4.1、规划

IP| 端口号|    角色|
----|----|--------|
192.168.223.129|6380|Redis Master|
192.168.223.129|6381|Redis Slave|
192.168.223.129|6382|Sentinel


### 4.2、 复制配置文件
```bash
[root@localhost redis-5.0.7]# mkdir redis_sentinel
[root@localhost redis-5.0.7]# cp redis.conf redis_sentinel/redis_6380.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_sentinel/redis_6381.conf
[root@localhost redis-5.0.7]# touch redis_sentinel/sentinel.conf
```
### 4.3、修改redis_6380.conf配置文件
用以下属性覆盖配置文件中的属性(与3.2的配置类似，只是端口不同)
```conf
# 后台启动。
daemonize yes

# 否则只能通过本机访问redis。
protected-mode no

# 绑定服务器真实ip。
bind 192.168.223.129

# 进程ID文件。
pidfile /var/run/redis_6380.pid

# 绑定端口。
port 6380

# 日志文件。
logfile log_6380.log

# dump文件名。
dbfilename dump_6380.rdb

# 客户端闲置多少秒后，断开连接。
timeout 300

# 日志级别，支持debug，verbose，notice，warning。
loglevel notice

# 最大客户端链接数。
maxclients 1000

# 如不开启可以不配置。
# requirepass 123456

# 每秒一次同步磁盘。
appendfsync everysec

# 最大内存，单位字节，4294967296字节=4GB。
maxmemory 4294967296

# 关闭日志记录（严格不丢数据的场合应该配置成yes）
appendonly no
```

### 4.4、修改redis_6381.conf配置文件
用以下属性覆盖配置文件中的属性(与3.2的配置类似，只是端口不同，最后多了一个slaveof)
```conf
# 后台启动。
daemonize yes

# 否则只能通过本机访问redis。
protected-mode no

# 绑定服务器真实ip。
bind 192.168.223.129

# 进程ID文件。
pidfile /var/run/redis_6381.pid

# 绑定端口。
port 6381

# 日志文件。
logfile log_6381.log

# dump文件名。
dbfilename dump_6381.rdb

# 客户端闲置多少秒后，断开连接。
timeout 300

# 日志级别，支持debug，verbose，notice，warning。
loglevel notice

# 最大客户端链接数。
maxclients 1000

# 如不开启可以不配置。
# requirepass 123456

# 每秒一次同步磁盘。
appendfsync everysec

# 最大内存，单位字节，4294967296字节=4GB。
maxmemory 4294967296

# 关闭日志记录（严格不丢数据的场合应该配置成yes）
appendonly no

# redis master的 ip port，表示当前节点是指定ip port的从节点。
slaveof 192.168.223.129 6380
```

### 4.5、修改sentinel.conf配置文件
```conf
# 后台启动。
daemonize yes

# 否则只能通过本机访问redis。
protected-mode no

# 绑定服务器真实ip。
bind 192.168.223.129

# 绑定端口。
port 6382

# 日志文件。
logfile log_6382.log

# sentinel monitor [被监控数据库(master)名字(自定义的名称)] [master的ip] [master的端口] [主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机]
sentinel monitor mymaster 192.168.223.129 6380 1

# 若开启密码认证，则设置为master的密码
# sentinel auth-pass mymaster 123456
```


### 4.6、启动
```bash
# 启动master
[root@localhost redis_sentinel]# /opt/root/module/redis-5.0.7/src/redis-server /opt/root/module/redis-5.0.7/redis_sentinel/redis_6380.conf

# info replication
[root@localhost redis_sentinel]# redis-cli -h 192.168.223.129 -p 6380
192.168.223.129:6380> ping
PONG
192.168.223.129:6380> info replication
# Replication
role:master
connected_slaves:0
master_replid:840f08cab2f087bb3c660e337aa326cfb779a1c0
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
192.168.223.129:6380> set k1 v1
OK
192.168.223.129:6380> get k1
"v1"
192.168.223.129:6380> quit

# 启动slave
[root@localhost redis_sentinel]# /opt/root/module/redis-5.0.7/src/redis-server /opt/root/module/redis-5.0.7/redis_sentinel/redis_6381.conf

# info replication
[root@localhost redis_sentinel]# redis-cli -h 192.168.223.129 -p 6380
192.168.223.129:6380> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=192.168.223.129,port=6381,state=online,offset=14,lag=1
master_replid:4b7dca0aa8b5f2b2c4d85fb1576e49919c915ddc
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
192.168.223.129:6380> 

# info replication
[root@localhost redis_sentinel]# redis-cli -h 192.168.223.129 -p 6381
192.168.223.129:6381> info replication
# Replication
role:slave
master_host:192.168.223.129
master_port:6380
master_link_status:up
master_last_io_seconds_ago:0
master_sync_in_progress:0
slave_repl_offset:1064
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:4b7dca0aa8b5f2b2c4d85fb1576e49919c915ddc
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:1064
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:1064

# 启动sentinel
[root@localhost redis_sentinel]# /opt/root/module/redis-5.0.7/src/redis-sentinel /opt/root/module/redis-5.0.7/redis_sentinel/sentinel.conf 
```

## 4.7、检查
```bash
[root@localhost redis_sentinel]# ps -ef | grep redis-server
root      6514     1  0 21:08 ?        00:00:04 /opt/root/module/redis-5.0.7/src/redis-server 192.168.223.129:6381                                       
root      6669     1  0 21:25 ?        00:00:00 /opt/root/module/redis-5.0.7/src/redis-server 192.168.223.129:6380                                       
root      6706  6682  0 21:27 pts/1    00:00:00 grep redis-server

[root@localhost redis_sentinel]# ps -ef | grep redis-sentinel
root      6659     1  0 21:24 ?        00:00:01 /opt/root/module/redis-5.0.7/src/redis-sentinel 192.168.223.129:6382 [sentinel]                          
root      6709  6682  0 21:27 pts/1    00:00:00 grep redis-sentinel

[root@localhost redis_sentinel]# redis-cli -h 192.168.223.129 -p 6380
192.168.223.129:6380> SHUTDOWN
not connected> quit

[root@localhost redis_sentinel]# redis-cli -h 192.168.223.129 -p 6381
192.168.223.129:6381> info replication
# Replication
role:master
connected_slaves:0
master_replid:66debe93a9dea3ed5a256973c8d1735d7531ca5b
master_replid2:4b7dca0aa8b5f2b2c4d85fb1576e49919c915ddc
master_repl_offset:7020
second_repl_offset:4118
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:7020

192.168.223.129:6381> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=192.168.223.129,port=6380,state=online,offset=7331,lag=1
master_replid:66debe93a9dea3ed5a256973c8d1735d7531ca5b
master_replid2:4b7dca0aa8b5f2b2c4d85fb1576e49919c915ddc
master_repl_offset:7331
second_repl_offset:4118
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:7331
```


## 5、集群模式
### 5.1、规划

IP| 端口号|    角色|
----|----|--------|
192.168.223.129|6383|Redis Master|
192.168.223.129|6384|Redis Master|
192.168.223.129|6385|Redis Master|
192.168.223.129|6386|Redis Slave|
192.168.223.129|6387|Redis Slave|
192.168.223.129|6388|Redis Slave|


### 5.2、 复制配置文件
```bash
[root@localhost redis-5.0.7]# mkdir redis_cluster
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6383.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6384.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6385.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6386.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6387.conf
[root@localhost redis-5.0.7]# cp redis.conf redis_cluster/redis_6388.conf
```
### 5.3、修改redis_6383.conf配置文件
用以下属性覆盖配置文件中的属性(与3.2的配置类似，只是端口不同，并在最后增加了4个集群相关的配置)
```conf
# 后台启动。
daemonize yes

# 否则只能通过本机访问redis。
protected-mode no

# 绑定服务器真实ip。
bind 192.168.223.129

# 进程ID文件。
pidfile /var/run/redis_6383.pid

# 绑定端口。
port 6383

# 日志文件。
logfile log_6383.log

# dump文件名。
dbfilename dump_6383.rdb

# 客户端闲置多少秒后，断开连接。
timeout 300

# 日志级别，支持debug，verbose，notice，warning。
loglevel notice

# 最大客户端链接数。
maxclients 1000

# 如不开启可以不配置。
# requirepass 123456

# 每秒一次同步磁盘。
appendfsync everysec

# 最大内存，单位字节，4294967296字节=4GB。
maxmemory 4294967296

# 关闭日志记录（严格不丢数据的场合应该配置成yes）
appendonly no

# 集群相关配置
# 开启集群
cluster-enabled yes
# 集群配置文件名称，每个实例配置的要不同，redis会根据文件名自动新建
cluster-config-file nodes-6383.conf
cluster-node-timeout 15000
cluster-require-full-coverage no
```

另外5个配置文件与redis_6383.conf类似，只是端口不同，这里不再赘述。

### 5.4、启动集群所有节点
保证所有节点单独启动正常，cli连接正常。
```bash
[root@localhost redis_cluster]# /opt/root/module/redis-5.0.7/src/redis-server /opt/root/module/redis-5.0.7/redis_cluster/redis_6383.conf

[root@localhost redis_cluster]# ps -ef | grep redis-server
root      9794     1  3 12:52 ?        00:00:00 /opt/root/module/redis-5.0.7/src/redis-server 192.168.223.129:6383 [cluster]                            
root      9799  8823  0 12:52 pts/2    00:00:00 grep redis-server

[root@localhost redis_cluster]# redis-cli -h 192.168.223.129 -p 6383
192.168.223.129:6383> ping
PONG
```

## 5.5、创建集群
```bash
[root@localhost redis_cluster]# /opt/root/module/redis-5.0.7/src/redis-cli -h 192.168.223.129 -p 6383 --cluster create 192.168.223.129:6383 192.168.223.129:6384 192.168.223.129:6385 192.168.223.129:6386 192.168.223.129:6387 192.168.223.129:6388 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.223.129:6387 to 192.168.223.129:6383
Adding replica 192.168.223.129:6388 to 192.168.223.129:6384
Adding replica 192.168.223.129:6386 to 192.168.223.129:6385
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: cc294f4ce844777262dc7b4eb634f7c40f026102 192.168.223.129:6383
   slots:[0-5460] (5461 slots) master
M: 61a8f57a0af4f8ccfcd3e477c80650cca81a5e16 192.168.223.129:6384
   slots:[5461-10922] (5462 slots) master
M: 8e7dec91493bb25b57959fa8041b94bb28bf8235 192.168.223.129:6385
   slots:[10923-16383] (5461 slots) master
S: 69d820e40243aabf461370911311e7717c72e35b 192.168.223.129:6386
   replicates cc294f4ce844777262dc7b4eb634f7c40f026102
S: 0749a30a2e93e2ba38ed6160aa8f93d3c1ccf3c7 192.168.223.129:6387
   replicates 61a8f57a0af4f8ccfcd3e477c80650cca81a5e16
S: 6d0606404273f011234b7e99afe5462b4ea362b0 192.168.223.129:6388
   replicates 8e7dec91493bb25b57959fa8041b94bb28bf8235
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
....
>>> Performing Cluster Check (using node 192.168.223.129:6383)
M: cc294f4ce844777262dc7b4eb634f7c40f026102 192.168.223.129:6383
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 69d820e40243aabf461370911311e7717c72e35b 192.168.223.129:6386
   slots: (0 slots) slave
   replicates cc294f4ce844777262dc7b4eb634f7c40f026102
M: 8e7dec91493bb25b57959fa8041b94bb28bf8235 192.168.223.129:6385
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 0749a30a2e93e2ba38ed6160aa8f93d3c1ccf3c7 192.168.223.129:6387
   slots: (0 slots) slave
   replicates 61a8f57a0af4f8ccfcd3e477c80650cca81a5e16
M: 61a8f57a0af4f8ccfcd3e477c80650cca81a5e16 192.168.223.129:6384
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 6d0606404273f011234b7e99afe5462b4ea362b0 192.168.223.129:6388
   slots: (0 slots) slave
   replicates 8e7dec91493bb25b57959fa8041b94bb28bf8235
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

## 5.6、检查
```bash
[root@localhost redis_cluster]# /opt/root/module/redis-5.0.7/src/redis-cli -h 192.168.223.129 -p 6383
192.168.223.129:6383> CLUSTER info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:139
cluster_stats_messages_pong_sent:151
cluster_stats_messages_sent:290
cluster_stats_messages_ping_received:146
cluster_stats_messages_pong_received:139
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:290
192.168.223.129:6383> CLUSTER nodes
69d820e40243aabf461370911311e7717c72e35b 192.168.223.129:6386@16386 slave cc294f4ce844777262dc7b4eb634f7c40f026102 0 1583227670623 4 connected
8e7dec91493bb25b57959fa8041b94bb28bf8235 192.168.223.129:6385@16385 master - 0 1583227668000 3 connected 10923-16383
0749a30a2e93e2ba38ed6160aa8f93d3c1ccf3c7 192.168.223.129:6387@16387 slave 61a8f57a0af4f8ccfcd3e477c80650cca81a5e16 0 1583227667593 5 connected
61a8f57a0af4f8ccfcd3e477c80650cca81a5e16 192.168.223.129:6384@16384 master - 0 1583227669614 2 connected 5461-10922
6d0606404273f011234b7e99afe5462b4ea362b0 192.168.223.129:6388@16388 slave 8e7dec91493bb25b57959fa8041b94bb28bf8235 0 1583227669000 6 connected
cc294f4ce844777262dc7b4eb634f7c40f026102 192.168.223.129:6383@16383 myself,master - 0 1583227668000 1 connected 0-5460
```


## 6、使用cli关闭redis
* 单机例关闭
```bash
[root@localhost bin]# redis-cli shutdown
```

* 非单机关闭，指定ip、端口关闭
```bash
[root@localhost bin]# redis-cli -h 192.168.223.129 -p 6379 shutdown
```





