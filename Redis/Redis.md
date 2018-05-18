# Redis
## Redis-Cluster
* 集群是采用`hash桶`即`slot`的方式对数据进行分发存储的
  * 集群总共slot数量是`16384`个，`Redis`会对`key`进行`CRC16 MOD 16384`计算得到`slot`编号，再存入到对应的`slot`中；
  * 集群又会把`16384`平分到每个节点上（`16384/N`）；