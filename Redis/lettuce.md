# lettuce 学习记录

## 知识点
* `Redis`的连接是`长连接`并`线程安全`的；
* 如果链接丢失，会自动重连接；
* 执行中并且没有超时的命令，会在重连接后再次发送指令；
* 每个连接都有一个默认超时时间（创建client的时候可以设置，默认是60s）；
* 所有异常父类——`RedisException`
* `同步`状态下，如果Redis返回错误，框架会抛出异常`RedisCommandExecutionException`，但是`异步`状态下，不会抛出异常；
* **`Redis URI`**支持的格式：
  * `redis://[password@]host[:port][/databaseNumber]`   一般形式
  * `rediss://[password@]host[:port][/databaseNumber]`  SSL形式(3.1开始支持单例模式，4.2开始支持集群模式)
    * 此框架（`Lettuce`没有实现SSL，而是使用的[`stunnel`][stunnel]工具）
    * [SSL连接-详细文档][SSL]
  * `redis-sentinel://[password@]host[:port][,host2[:port2]][/databaseNumber]#sentinelMasterId` [Redis-Sentinel形式](https://blog.csdn.net/men_wen/article/details/72724406)
  * `redis-socket:///tmp/redis`  [Unix Domain Socket][UDS] connection to local Redis;
    *   `Unix Domain Socket`只对本地的单例模式的Redis有效，对集群没用；但是集群可以使用`RedisClusterClient `，实现对本地节点使用`Unix Domain Socket`，集群的其他节点使用TCP；
* `Redis Sentinel`和`Redis Master/Slave`不支持SSL
* 使用SSL获取连接之前会有一个握手（`handshake`），如果握手发生错误，会抛出`RedisConnectionExceptions`；
* 可以使用`redisClient.setOptions`对client进行设置，但是当`connection`创建完成之后，`ClientOption`就不可再变；即使修改Option也不会影响client
  * 还可使用其他2种方式：
      > lettuce uses Java defaults for the `trust store` that is usually `cacerts` in your `jre/lib/security` directory and comes with customizable SSL options via `Client options`.  
        ① If you need to add your own root certificate, so you can configure `SslOptions`, import it either to `cacerts` or ② you provide an own trust store and set the necessary system properties.
* 默认不支持`TLS`，可以通过`redisUri.setStartTls(true)`打开；<br> <br> <br> 
  
***

* **创建`RedisClient`实例**：
  * 方式一：
      ````java
        RedisURI redisUri = RedisURI.create("redis://authentication@localhost/2");
        RedisClient client = RedisClient.create(rediUri);
      ````
  * 方式二：
      ````java
        RedisURI redisUri = RedisURI.Builder.redis("localhost")
                                        .withSsl(true)   // SSL
                                        .withPassword("authentication")
                                        .withDatabase(2)
                                        .build();
        RedisClient client = RedisClient.create(rediUri);
      ````
  * 方式三：
      ````java
        RedisClient redisClient = RedisClient.create("redis://192.168.76.130:6379/0");
        StatefulRedisConnection<String, String> connection = redisClient.connect();
      ````
* **[`Client options`][ClientOptions]** 
  * `Since: 3.1, 4.0`：实现连接之前进行`PING`检测，如果ping失败，那么就表示一定连接失败；  
* **[`Redis Cluster`][RedisCluster]** 
  * 连接`集群`的时候，不用填写所有节点的URI，只连接其中的一个节点，然后会自动感知`Redis集群`的`拓扑（topology）`结构，连接到其他节点；
  * `lettuce`会为每一个节点建立一个`connection`；每一个连接（`connection`）会绑定到特定的`nodeId`或`host/port`上，即使`nodeId`所对应的`host/port`变动了；
  * `lettuce`支持一些`Cross-slot`操作；
  * 获取所有key和所有节点（必须再`异步`模式下）：
    * `lettuce`支持选择部分节点（放入一个集合，eg：Set），然后在集合（Set）中执行命令；
        ```java
            RedisAdvancedClusterAsyncCommands<String, String> async = clusterClient.connect().async();
            AsyncNodeSelection<String, String> slaves = connection.slaves();
            
            AsyncExecutions<List<String>> executions = slaves.commands().keys("*");
            executions.forEach(result -> result.thenAccept(keys -> System.out.println(keys)));
        ```
  * `lettuce`支持在Runtime时向集群中添加节点；`RedisClusterClient`管理着集群的拓扑结构；
* 同步/异步


[RedisCluster]:https://github.com/lettuce-io/lettuce-core/wiki/Redis-Cluster  "集群连接"
[ClientOptions]:https://github.com/lettuce-io/lettuce-core/wiki/Client-options  "各种client参数设置"
[UDS]:https://blog.csdn.net/z2066411585/article/details/78966434/ "Unix进程间通信"
[SSL]:https://github.com/lettuce-io/lettuce-core/wiki/SSL-Connections
[stunnel]:https://github.com/StarzoneCN/summary-documents/blob/master/%E7%BD%91%E7%BB%9C/stunnel-SSL%E5%B7%A5%E5%85%B7.md