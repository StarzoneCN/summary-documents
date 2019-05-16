# <div style="text-align:center;color:#FF9900">Redis二级索引</div>
## 1. 简介
一般地讲，Redis是只提供了通过`key`查询`value`，但是，当使用复杂结构（如：`hash`）时，Redis就不是纯粹的`key-value`数据库，此时Redis提供了二级索引（可以是单列，也可以是多列-复合索引），以提高redis的查询的效率；
## 2. Sorted Set
### 2.1 简单数字排序
`sorted set`有2个与二级索引相关的命令`ZADD`和`ZRANGEBYSCORE`，命令格式如下：
```redis
ZADD key score value

ZRANGEBYSCORE key 0 1000   -- 取出score在0-1000以内的值
```
`Sorted Set`会根据`socre`创建二级索引，提高查询性能；




















### 参考资料
[Secondary indexing with Redis](https://redis.io/topics/indexes)
