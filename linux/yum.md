# <div style="text-align:center;color:#FF9900">yum</div>

## 1. 介绍
服务器端：
> 所有要发行的rpm包都放在yum服务器上以提供别人来下载，rpm包根据kernel的版本号，cpu的版本号分别编译发布。yum服务器只要提供简单的下载就可以了，ftp或者httpd的形式都可以。yum服务器有一个最重要的环节就是整理出每个rpm包的基本信息，包括rpm包对应的版本号，conf文件，binary信息，以及很关键的依赖信息。在yum服务器上提供了createrepo工具，用于把rpm包的基本概要信息做成一张"清单"，这张"清单""就是描述每个rpm包的spec文件中信息。

客户端：
> client每次调用yum install或者search的时候，都会去解析`/etc/yum.repos.d`下面所有以`.repo`结尾的配置文件，这些配置文件指定了yum服务器的地址。yum会定期去"更新"yum服务器上的rpm包"清单"，然后把"清单"下载保存到yum自己的cache里面，根据`/etc/yum.conf`里配置(默认是在`/var/cache/yum`下面)，每次调用yum装包的时候都会去这个cache目录下去找"清单"，根据"清单"里的rpm包描述从而来确定安装包的名字，版本号，所需要的依赖包等，然后再去yum服务器下载rpm包安装。(前提是不存在rpm包的cache)
