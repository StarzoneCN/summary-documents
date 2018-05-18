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
  * `rediss://[password@]host[:port][/databaseNumber]`  SSL形式
  * `redis-sentinel://[password@]host[:port][,host2[:port2]][/databaseNumber]#sentinelMasterId` [Redis-Sentinel形式](https://blog.csdn.net/men_wen/article/details/72724406)
  * `redis-socket:///path/to/socket`  Unix Domain Socket connection to Redis
* 创建`RedisClient`实例：
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
  * 方式三：
      ````java
        RedisClient redisClient = RedisClient.create("redis://192.168.76.130:6379/0");
        StatefulRedisConnection<String, String> connection = redisClient.connect();
      ````