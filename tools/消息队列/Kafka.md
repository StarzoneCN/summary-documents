# <div style="text-align:center;color:#FF9900">Kafka</div>

### 1. 建议：
* `partition`的数量大于`consumer`的数量，因为 **一个partition只能被一个消费者消费（一个消费者可以同时消费多个partition）**;
* `partition`的数量大于集群`broker`的数量，**这样leader partition就可以均匀的分布在各个broker中，最终使得集群负载均衡**
* **`kafka`需要为每个`partition`分配一些内存来缓存消息数据**，如果partition数量越大，就要为kafka分配更大的heap space;
* Kafka**没有限定单个消息的大小**，但我们推荐消息大小**不要超过1MB**（参考：[一文看懂Kafka消息格式的演变][]-搜索“Varints”）,通常一般消息大小都在**1~10kB**之间。
* kafka集群，尽量与hadoop集群分开，因为**kafka依赖磁盘读写和大的页面缓存，如果和hadoop共享节点的话会影响其使用页面缓存的性能**。
* 推荐采用专用的服务器来部署kafka集群，避免其他应用影响磁盘性能；
* kafka依赖zookeeper，zookeeper集群越大其读写性能越慢；
* 一般的来说，kafka集群瓶颈在于网络和磁盘吞吐量，所以我们先评估一下集群的网络和磁盘需求；
* `kafka-0.11.0`之后


### 2. 知识点
* Producers可以`异步的`并行的向kafka发送消息，但是通常producer在发送完消息之后会得到一个future响应，返回的是offset值或者发送过程中遇到的错误。这其中有个非常重要的参数`acks`,这个参数决定了**producer要求leader partition 收到确认的副本个数**，如果`acks`设置数量为`0`，表示**producer不会等待broker的响应**，所以，producer无法知道消息是否发送成功，这样有可能会导致数据丢失，但同时，**acks值为0会得到最大的系统吞吐量**。若`acks`设置为`1`，表示producer会在leader partition收到消息时得到broker的一个确认，这样会有更好的可靠性，因为客户端会等待直到broker确认收到消息。若设置为`-1`，producer会在所有备份的partition收到消息时得到broker的确认，这个设置可以得到**最高的可靠性保证**。
* Kafka 消息有一个`定长的header`和`变长的字节数组`组成；
* Producer端可以通过`GZIP`或`Snappy`或`ZigZag(kafka-0.11.0+)`格式对消息集合进行压缩；
* 采用`sendfile`/`zero-copy`；
* 经验证，**顺序写磁盘效率比随机写内存还要高**，这是Kafka高吞吐率的一个很重要的保证;




参考：
[Kafka 设计与原理详解][]（推）、[kafka存储结构][]、[kafka持久化原理][]、[观察者模式与发布订阅模式微妙区别][]、[官方文档-英文][]、[一文看懂Kafka消息格式的演变][]（精）

[Kafka 设计与原理详解]:https://blog.csdn.net/suifeng3051/article/details/48053965
[kafka存储结构]:https://blog.csdn.net/yaolong336/article/details/80047701
[kafka持久化原理]:https://blog.csdn.net/wingofeagle/article/details/60965929
[观察者模式与发布订阅模式微妙区别]:https://juejin.im/post/5a14e9edf265da4312808d86
[官方文档-英文]:http://kafka.apache.org/documentation.html#topic-config
[一文看懂Kafka消息格式的演变]:https://blog.csdn.net/u013256816/article/details/80300225
