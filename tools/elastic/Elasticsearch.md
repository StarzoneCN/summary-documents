# <div style="text-align:center;color:#FF9900">Elasticsearch</div>

## 1. 概念

### 1.1 Node 与 Cluster

> Elastic 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。

### 1.2 Index

> An index is a collection of documents that have somewhat similar characteristics.
>
> index是具有相似特征的document的集合；

> Elastic 会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。所以，Elastic 数据管理的顶层单位就叫做 Index（索引）。它是单个数据库的同义词。每个 Index （即数据库）的名字必须是**小写**。

查看所有index：

```shell
curl -X GET 'localhost:9200/_cat/indices?v'
```

### 1.3 Document

> Index 里面单条的记录称为 Document（文档，json格式表示）。许多条 Document 构成了一个 Index。
>
> 同一个 Index 里面的 Document，不要求有相同的结构（scheme），但是最好保持相同，这样有利于提高搜索效率。

### 1.4 Type

Document 可以分类，这个type就是分类（category/partition）标志；

列出每个index包含的type：

```shell
curl 'localhost:9200/_mapping?pretty=true'
```

> 根据[规划](https://www.elastic.co/blog/index-type-parent-child-join-now-future-in-elasticsearch)，Elastic 6.x 版只允许每个 Index 包含一个 Type，7.x 版将会彻底移除 Type。

比如：IT博客分类-java/c/python/html等；

### 1.5 Near Real Time (NRT)

Elasticsearch是一个近乎实时的查询平台，这以为这从document被添加到index到可以查询，会有一个微小的延迟（通常是1秒）；

### 1.6 cluster

集群的名称(默认elasticsearch)最好唯一，因为如果你设置了node通过集群名称来加入集群，而集群名称不唯一，就会导致node添加混乱；

### 1.7 Node

一个节点就是一个elasticsearch实例；Node默认是通过name进行区别的，这个name是在node启动的时候随机生成的UUID字符串；

### 1.8 shards

碎片化，单节点存储能力毕竟有限，分片/碎片化是必然的选择；如何分片及分片数据如何汇总，这些都是elasticsearch处理，对用户来说是透明的；

> Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.

优点：

1. 拆分空间
2. 分片后，多片之间可以并行处理，挺高性能；

> Each Elasticsearch shard is a Lucene index. There is a maximum number of documents you can have in a single Lucene index. As of [`LUCENE-5843`](https://issues.apache.org/jira/browse/LUCENE-5843), the limit is `2,147,483,519` (= Integer.MAX_VALUE - 128) documents. shard查看接口[`_cat/shards`](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/cat-shards.html) 
>
> ```shell
> curl get 'localhost:9200/_cat/shards/{_index}'
> ```

### 1.9 replica

为了高可用性，elasticsearch实现了故障转移，elasticsearch允许对shards进行备份/复制(replica)；

优点：

1. 实现高可用；
2. 备份也可以执行搜索，并且是并行执行的，如此便提高了elasticsearch的吞吐量；

`replica`（默认1个）和`shards`（默认5个）的数量可以再index创建的时候进行设置；replica的数量可以动态改变，但是shards数量不能动态变化；



## 2. 知识点

### 2.1 后台运行

```shell
elasticsearch -d
```

### 2.2 外网访问

方法：修改config/elasticsearch.yml中的network.host为network.host: 0.0.0.0

这样会报如下错：

```console
(1) max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
(2) max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

解决方法：

1. `/etc/security/limits.conf`文件末尾添加

   ```
   * soft nofile 300000
   * hard nofile 300000
   * soft nproc 102400
   * soft memlock unlimited
   * hard memlock unlimited
   ```

   添加后：

   ```
   #@student        -       maxlogins       4
   
   * soft nofile 300000
   * hard nofile 300000
   * soft nproc 102400
   * soft memlock unlimited
   * hard memlock unlimited
   # End of file
   ```

2. 切换至root用户，执行

   ```shell
   sysctl -w vm.max_map_count=262144
   ```

   这样是临时设置，重启后又会失效，永久性的做法是：

   1. 修改`/etc/sysctl.conf `文件中的`vm.max_map_count `参数为262144或更大；
   2. 执行`sysctl -p`；

### 2.3 score

* `must`和`should`对分数有影响；

  > The `bool` query takes a *more-matches-is-better* approach, so the score from each matching `must` or `should` clause will be added together to provide the final `_score` for each document.

* filter下的元素对分数无影响：

  > Queries specified under the `filter` element have no effect on scoring — scores are returned as `0`

## 3. API

一条数据形式如下：

```json
{
    "_index": "filebeat-6.5.4-2019.01.17",
    "_type": "doc",
    "_id": "wF_SWmgBPWkCaTU3iIFa",
    "_version": 1,
    "found": true,
    "_source": {
        "host": {
            "architecture": "x86_64",
            "os": {
                "version": "7 (Core)",
                "family": "redhat",
                "codename": "Core",
                "platform": "centos"
            },
            "id": "bbd62b2a6ca84d5ebf223db018c3c6f0",
            "containerized": true,
            "name": "bogon"
        },
        "input": {
            "type": "log"
        },
        "@timestamp": "2019-01-17T07:59:31.339Z",
        "beat": {
            "version": "6.5.4",
            "hostname": "bogon",
            "name": "bogon"
        },
        "prospector": {
            "type": "log"
        },
        "tags": [
            "beats_input_codec_plain_applied"
        ],
        "offset": 4227,
        "@version": "1",
        "source": "/home/hongxing/workspace/jars/demo-spring-boot.log",
        "message": "[WARN ] 2019-01-17 15:59:28.047 [http-nio-8088-exec-8] IndexController - 蹉跎岁月"
    }
}
```

* `_source`: 表示存储的数据；

### 3.1 新建index

index相当于一个数据库；如新建一个名为accounts的index（**注意**request方法）：

```shell
curl -X PUT "localhost:9200/accounts?pretty"
```

### 3.2 数据操作

```shell
curl -X PUT 'localhost:9200/accounts' -d '
{
  "mappings": {
    "person": {
      "properties": {
        "user": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "title": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "desc": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        }
      }
    }
  }
}'
```

#### 3.2.1 新增

向指定type新增数据，如果index-accounts不存在，elasticsearch会自动创建：

```shell
$ curl -X PUT 'localhost:9200/accounts/person/1' -d '
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}'
```

解释：`index`是accounts，`type`是person，`1`表示记录id，如果不指定id，会随机生成一个字符串，如下（注意请求方法）：

```shell
$ curl -X POST 'localhost:9200/accounts/person' -d '
{
  "user": "李四",
  "title": "工程师",
  "desc": "系统管理"
}'
```

#### 3.2.2 查看

向`/Index/Type/Id`发出 GET 请求，就可以查看这条记录：

```shell
curl 'localhost:9200/accounts/person/1?pretty=true'
```

#### 3.2.3 删除

删除记录就是发出 DELETE 请求：

```shell
curl -X DELETE 'localhost:9200/accounts/person/1'
```

> It is worth noting that it is much more efficient to delete a whole index instead of deleting all documents with the Delete By Query API.

#### 3.2.4 更新

```shell
curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe" }
}'
```

elasticsearch的更新时限删除数据，然后再添加数据，不存在直接修改更新既有数据；

添加字段：

```shell
curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
```

字段累加：

```shell
curl -X POST "localhost:9200/customer/_doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "script" : "ctx._source.age += 5"
}'
```

> ctx._source代表文档的\_source

#### 3.2.4 查询

使用 GET 方法，直接请求`/Index/Type/_search`，就会返回所有记录：

```shell
curl 'localhost:9200/accounts/person/_search'
```

#### 3.2.5 全文搜索

[查询语法](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl.html)，[request-body](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/search-request-body.html)如下get方法，并且只返回`_source`中的（"account_number", "balance", "desc"）三个字段：

```shell
curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件" }},
  "_source": ["account_number", "balance", "desc"]
}'
```

还可以[uri](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/search-uri-request.html)方式请求：

```shell
curl -X GET "localhost:9200/twitter/_search?q=user:kimchy"
```

查询句子，如下面的例子：搜索address包含“mill lane"

```shell
curl -X GET "localhost:9200/bank/_search" -H 'Content-Type: application/json' -d'
{
  "query": { "match_phrase": { "address": "mill lane" } }
}
'
```



#### 3.2.6 类似分页

Elastic 默认一次返回10条结果，可以通过`size`字段改变这个设置：

```shell
curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理" }},
  "size": 1
}'
```

还可以通过`from`字段，指定位移:

```shell
curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理 实施" }},
  "from": 1,
  "size": 1
}'
```

`管理 实施`表示`or`关系；

#### 其他API

* `/_search`

  ```js
  GET /_search
  {
      "query" : {
          "term" : { "user" : "kimchy" }
      }
  }
  ```

* 几个费解的API：

  1.  [`_delete_by_query` API](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/docs-delete-by-query.html) 
  2. [`docs-update-by-query` API](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/docs-update-by-query.html) 

### 3.3 状态API

### 3.4 health

```
GET /_cat/health?v
```

返回：

```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1547781396 03:16:36  elasticsearch yellow          1         1     10  10    0    0        9             0                  -                 52.6%

```

**status颜色**：

* `green` - everything is ok
* `yellow` - means all data is available but some replicas are not yet allocated (cluster is fully functional)
* `red` - means some data is not available for whatever reason. 但是部分功能可能是好的；

### 3.5 Nodes

显示集群节点：

```shell
curl get 'localhost:9200/_cat/nodes?v'
```

### 3.6 indeces

显示多有index

```shell
curl -X GET "localhost:9200/_cat/indices?v"
```

### 3.7 [批量操作API](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/docs-bulk.html)

```shell
curl -X POST "localhost:9200/customer/_doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
```

示例解释：把`_id`等于1的document的`_source`更新为`{"doc": { "name": "John Doe becomes Jane Doe" } }`，删除`_id`为2的记录；

**注意**：每个json内部不能换行，json结束后换行，最后一个json结束后也要换行；







### so more





## 4. 查询DSL

查询是否有效，取决于查询语句是否在“query”上下文或“filter”上下文中：

> The behaviour of a query clause depends on whether it is used in *query context* or in *filter context*:
>
> * query context: *How well does this document match this query clause?*
> * filter context: *Does this document match this query clause?*

下面的是一个query结果：

* `_score`：表示查询结果匹配度；

```json
{
    "took": 4,
    "timed_out": false,
    "_shards": {
        "total": 3,
        "successful": 3,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": 12,
        "max_score": 3.506558,
        "hits": [
            {
                "_index": "filebeat-6.5.4-2019.01.17",
                "_type": "doc",
                "_id": "vF-5WmgBPWkCaTU3DIEO",
                "_score": 3.506558,
                "_source": {
                    "host": {
                        "architecture": "x86_64",
                        "os": {
                            "version": "7 (Core)",
                            "family": "redhat",
                            "codename": "Core",
                            "platform": "centos"
                        },
                        "id": "bbd62b2a6ca84d5ebf223db018c3c6f0",
                        "containerized": true,
                        "name": "bogon"
                    },
                    "input": {
                        "type": "log"
                    },
                    "@timestamp": "2019-01-17T07:31:41.080Z",
                    "beat": {
                        "version": "6.5.4",
                        "hostname": "bogon",
                        "name": "bogon"
                    },
                    "prospector": {
                        "type": "log"
                    },
                    "tags": [
                        "beats_input_codec_plain_applied"
                    ],
                    "offset": 3907,
                    "@version": "1",
                    "source": "/home/hongxing/workspace/jars/demo-spring-boot.log",
                    "message": "[FATAL] 2019-01-17 15:31:34.542 [http-nio-8088-exec-2] IndexController - 明天"
                }
            }
        ]
    }
}
```

* 高频filter会自动被elasticsearch缓存；

* `filter context`形式：

  * `filter` or `must_not` parameters in the [`bool`](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-bool-query.html) query

    ```json
    GET /_search
    {
      "query": { 
        "bool": { 
          "must": [
            { "match": { "title":   "Search"        }}, 
            { "match": { "content": "Elasticsearch" }}  
          ]
        }
      }
    }
    ```

  * the `filter` parameter in the [`constant_score`](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-constant-score-query.html)query

  * the [`filter`](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/search-aggregations-bucket-filter-aggregation.html) aggregation

### 4.2 should

> - **should**子句：文档应该匹配should子句查询的一个或多个；
>
> 通常情况下，should子句是数组字段，包含多个should子查询，默认情况下，匹配的文档必须满足其中一个子查询条件。如果查询需要改变默认匹配行为，查询DSL必须显式设置布尔查询的参数minimum_should_match的值，该参数控制一个文档必须匹配的should子查询的数量，我遇到一个布尔查询语句，其should子句中包含两个查询，如果不设置参数minimum_should_match，其默认值是0。
>
> > If this query is used in a filter context and it has `should` clauses then at `least one` `should` clause is required to match.

```json
"should" : [
        {  "term" : { "tag" : "azure" } },
        {  "term" : { "tag" : "elasticsearch" } },
        {  "term" : { "tag" : "cloud" } }
    ],
"minimum_should_match" : 2
```

复杂的分组查询，例如：(A and B) or (C and D) or (E and F) ，把布尔查询作为should子句的一个子查询：

### 4.3 named query

关联查询条件与查询结果：

> If you need to know which of the clauses in the bool query matched the documents returned from the query, you can use [named queries](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/search-request-named-queries-and-filters.html) to assign a name to each clause.

## 5. 优化

### 5.1 filter

> 在布尔查询中，对查询结果的过滤，建议使用过滤（filter）子句和must_not子句，这两个子句属于过滤上下文（Filter Context），经常使用filter子句，使得ElasticSearch引擎自动缓存数据，当再次搜索已经被缓存的数据时，能够提高查询性能；由于过滤上下文不影响查询的评分，而评分计算让搜索变得复杂，消耗更多CPU资源，因此，filter和must_not查询减轻搜索的工作负载。