# <div style="text-align:center;color:#FF9900">Filebeat</div>

## 1. 介绍、原理



## 2. 配置

### 2.2 配置项

#### 2.2.1 shutdown_timeout

问题：**Filebeat如何确保至少投递一次**？

> Filebeat保证事件将被投递到配置的输出中至少一次，并且不会丢失数据。Filebeat能够实现这种行为，因为它将每个事件的投递状态存储在注册表文件中。
>
> 在定义的输出被阻塞且没有确认所有事件的情况下，Filebeat将继续尝试发送事件，直到输出确认收到事件为止。
>
> 如果Filebeat在发送事件的过程中关闭了，则在关闭之前它不会等待输出确认所有事件。当Filebeat重新启动时，发送到输出（但在Filebeat关闭前未确认）的任何事件将再次发送。这确保每个事件至少被发送一次，但是你最终可能会将重复的事件发送到输出。你可以通过设置`shutdown_timeout`选项，将Filebeat配置为在关闭之前等待特定的时间。

#### <a id="clean_remove">2.2.2 clean_remove</a>

清理被移除文件的状态缓存；

#### <a id="clean_inactive">2.2.3 clean_inactive</a>

清理不活跃文件的状态缓存；

#### 2.2.4 close_inactive

此项设置，以及[clean_remove](#clean_remove)、[clean_inactive](#clean_inactive)等在这篇[文章](http://www.manongjc.com/article/6633.html)中都有涉及到；



## 3. 实战

### 3.1 安装Elasticsearch

