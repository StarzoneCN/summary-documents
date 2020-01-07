# <div style="text-align:center;color:#FF9900">Redis</div>
## 1. Redis-Cluster
* **集群**是采用`hash桶`即`slot`的方式对数据进行分发存储的
  * 集群总共slot数量是`16384`个，`Redis`会对`key`进行`CRC16 MOD 16384`计算得到`slot`编号，再存入到对应的`slot`中；
  * 集群又会把`16384`平分到每个节点上（`16384/N`）；

## 2. Redis
### 2.1 命令
#### 2.1.1 启动server
`./redis-server /path/to/redis.conf`
#### 2.1.2 启动client
`redis-cli.exe -h 127.0.0.1 -p 6379`


### 2.2 设置
#### 2.2.1 redis.conf文件
##### 2.2.1.1 内存大小
不区分大小写
```shell
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
```
##### 2.2.1.2 文件引入
可以在主配置文件`redis.conf`中引入其他配置文件：
```Shell
# include /path/to/local.conf
# include /path/to/other.conf
```
**注意**：这种配置下，redis默认会按列出的顺序，后面文件覆盖前面文件的配置；
##### 2.2.1.3 daemon
默认`daemonize no`
> 如果此项设置为`yes`，redis运行时，默认会生成`/var/run/redis.pid`文件；
##### 2.2.1.4 pidfile
如果启动daemon模式，默认生成`/var/run/redis.pid`文件，可通过`pidfile`指令修改：`pidfile /var/run/redis.pid`
##### 2.2.1.5 端口
`port 6379`
##### 2.2.1.6 backlog
设置已经建立的TCP连接的最大数量： `tcp-backlog 511`；
**注意**：`/proc/sys/net/ipv4/tcp_max_syn_backlog`和`/proc/sys/net/core/somaxconn`都将影响到最大TCP连接数；
关于backlog，请移步[博客][backlog1]
##### 2.2.1.7 客户端IP过滤
`bind 192.168.1.100 10.0.0.1`
##### 2.2.1.8 UnixSocket
> 如果没有外网使用的需求，我们还可以让 Redis 以 Unix Socket 的方式运行，以避免 TCP/IP 的性能瓶颈，在高访问场景实现 25% 的性能提升
```Shell
# unixsocket /tmp/redis.sock
# unixsocketperm 700
```
*具体设置使用方式自行搜索*
##### 2.2.1.9 timeout
设置client失活多久后断开连接，单位秒（S），0表示设置无效：
`timeout 0`

##### 2.2.1.10 日志级别
`loglevel notice`
```Shell
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
```
##### 2.2.1.11 日志文件
`logfile ""`
* 空字符表示强制输出日志到`standard output`
* Note that if you use standard output for logging but daemonize, logs will be sent to `/dev/null`

##### 2.2.1.12 syslog
* 打开syslog
`syslog-enable yes` ： 使用`system logger`
* syslog identity
`syslog-ident redis`
##### 2.2.1.13 多库
`databases 16`
[知识扩展][databases1]
##### 2.2.1.14 保存到disk
详细解释请查阅`redis.conf`文件
```Shell
save 900 1
save 300 10
save 60 10000
```
##### 2.2.1.15 定时频率
查看`hz`配置属性；
##### 2.2.1.16 一致性延迟
查看`repl-disable-tcp-nodelay no`设置；
##### 2.2.1.17 复制积压缓冲区
查看`repl-backlog-size`配置；
##### 2.2.1.18 主从心跳检测
查看`repl-ping-slave-period`配置；



### 2.3 数据类型
#### 2.3.1 Set
* set类型会根据score进行排序，如果score相同，会进行字典排序；


##### 更多
TODO
1. `redis.conf`配置文件还有很多配置模块（`SNAPSHOTTING`、`REPLICATION`、`SECURITY`、`APPEND ONLY MODE`、`REDIS CLUSTER`、`SLOW LOG`、`LATENCY MONITOR`、`EVENT NOTIFICATION`、`ADVANCED CONFIG`），太多了，以后再搞吧！
2. redis可以通过Lua脚本实现原子性；

## 遗留疑问
1. Hash类型的value的属性数量较少时，编码类型是zipmap，这个zipmap具体内部原理是啥？

## 参考
[redis原理详解](https://blog.51cto.com/gudaoqing/1601114 "文章中的应用场景示例比较不错")
[redis为何采用单线程？](https://zhuanlan.zhihu.com/p/58038188)
[LFU淘汰策略](https://zhuanlan.zhihu.com/p/44651811)
[LFU淘汰策略2](https://yq.aliyun.com/articles/278922 '有介绍衰减算法')
[Redis持久化磁盘IO方式及其带来的问题](https://blog.51cto.com/gudaoqing/1601114)
[AOF重写](https://blog.csdn.net/hezhiqiang1314/article/details/69396887)
[redis分区](http://blog.jobbole.com/102194/)
[redis分区算法 - Consistent Hashing](http://blog.jobbole.com/101226/)
[redis“命名空间”](https://www.cnblogs.com/EasonJim/p/7818004.html)
[勿用redis的多库](http://blog.kankanan.com/article/52ff7528-redis-7684591a5e93.html)
[主从复制](https://www.cnblogs.com/kismetv/p/9236731.html)
[Redis 的多线程版本(keyDB)比 Redis 本身要快 5 倍](https://zhuanlan.zhihu.com/p/85884427)









[databases1]:http://blog.kankanan.com/article/52ff7528-redis-7684591a5e93.html
[backlog1]:https://blog.csdn.net/varyall/article/details/79681562
