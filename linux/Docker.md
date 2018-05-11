# <font color="red">Docker</font>
Docker 的基础是 Linux 容器（LXC）等技术。
Docker是一款针对程序开发人员和系统管理员来开发、部署、运行应用的一款虚拟化平台。
***
## 简介
#### Docker 组件
* Docker Engine
* Docker Hub
#### [Why is Docker？](http://www.dockerinfo.net/document "为什么选择Docker")
* 开发人员不必关心开发环境的变化；
* Docker很轻量，启动迅速；
* 多环境支持，多硬件支持；

## 镜像
#### 名词解释
* `基础镜像`：没有任何父镜像的镜像
* `镜像ID`: 所有镜像都是通过一个 64 位十六进制字符串 （内部是一个 256 bit 的值）来标识的。 为简化使用，前 12 个字符可以组成一个短ID，可以在命令行中使用。短ID还是有一定的 碰撞机率，所以服务器总是返回长ID。
#### 命令
* 获取镜像：`docker pull ubuntu:12.04` 或 `docker pull registry.hub.docker.com/ubuntu:12.04`
* 运行镜像： `ocker run -t -i ubuntu:12.04 /bin/bash`   
  * <font color="#E6A23C">*(注：如果不指定具体的标记，则默认使用 `latest` 标记信息)*</font>
* 列出本地镜像：`docker images`
* 提交更新后的副本：`docker commit -m "备注" -a "作者" 0b2616b0e5a8 ouruser/sinatra:v2`
  * `0b2616b0e5a8`是用来创建镜像的容器的 ID
