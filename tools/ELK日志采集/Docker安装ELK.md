# <div style="text-align:center;color:#FF9900">Docker安装ELK</div>

## 1. 安装步骤
要求：已经安装docker的Linux机器
### 1.1 拉取镜像
方案一：如果网络允许访问国外镜像服务器，可以参考镜像命令位置[docker.elastic.co][]，包含`Elasticsearch`、`Kibana`、`Filebeat`、`Logstash`以及更多镜像命令；
方案二：[向 Docker Hub提交 ELK 官方镜像方便国内拉取][]
方案三：
```sh
# 截止此稿，ELK镜像的版本号是7.3.0
$ docker pull elasticsearch:7.3.0
```








## & 问题及解决
1. 提示：`FORBIDDEN/12/index read-only / allow delete (api)`
   > elasticsearch.exceptions.AuthorizationException: AuthorizationException(403, 'cluster_block_exception', 'blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];')

   解决：
   ```sh
   curl -XPUT -H "Content-Type: application/json" http://localhost:9200/_all/_settings -d '{"index.blocks.read_only_allow_delete": null}'
   ```










[docker.elastic.co]:https://www.docker.elastic.co/
[向 Docker Hub提交 ELK 官方镜像方便国内拉取]:https://blog.csdn.net/enenand/article/details/81282424
