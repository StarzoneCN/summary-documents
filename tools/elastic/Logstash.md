# <div style="text-align:center;color:#FF9900">Logstash</div>
### 常用命令
* 检测配置文件正确性：
  ```sh
  $ ./bin/logstash -f first-pipeline.conf --config.test_and_exit
  ```
* 动态加载配置：
  ```sh
  $ ./bin/logstash -f first-pipeline.conf --config.reload.automatic
  ```
  如果没有设置自动加载配置，可以使用`kill -SIGHUP logstash-pid`命令，强制logstash实例加载更新配置；


### 解惑
* `pipelines.yml`文件是为了配置`multi pipeline`的;
* 关于Bytes类型有2种单位，SI(`k` `M` `G` `T` `P` `E` `Z` `Y`)是`1000进制`的，Binary (`Ki` `Mi` `Gi` `Ti` `Pi` `Ei` `Zi` `Yi`)是`1024进制`的；大小写不敏感；value和单位之间可以有空格；如果没有注明单位，默认`byte`；[参考](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html#bytes)
