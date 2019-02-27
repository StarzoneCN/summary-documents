# <div style="text-align:center;color:#FF9900">MariaDB</div>
## 1. 解压包安装
### 1.1 下载解压
[下载地址](https://downloads.mariadb.org/mariadb/10.3.12/#bits=64)
> Linux版本有三种：`通用型`、`CGLIB`、`Systemd`；以下演示通用型安装过程；

### 1.2 确认配置
MariaDB安装脚本(下一步讲)会自动查找`my.cnf`配置文件，读取其中的配置信息，根据配置信息进行安装；`my.cnf`的默认位置一般在`/etc/my.cnf`或`/etc/mysql/my.cnf`；也可以通过参数`--defaults-file=~/.my.cnf`手动指定配置文件位置，但是，这个参数必须是命令的`第一个`参数；

### 1.3 执行安装脚本
```shell
./scripts/mysql_install_db --user=mysql # 在MariaDB的解压目录下执行
```
#### 1.3.1 可能问题
```shell
# 报错：
./bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
```
解决：[参考](https://blog.51cto.com/cuchadanfan/1688129)
```Shell
yum install -y libaio
```

### 1.4 用户
#### 1.4.1 root密码
> root无初始密码

修改密码：
```Shell
./bin/mysqladmin -u root password 'new password'
```
### 1.X 问题汇总
#### 1.X.1  报错1130
连接时报错：
> 1130，ERROR 1130: Host xxx.xxx.xxx.xxx is not allowed to connect to this MySQL server

解决方法：授权
[参考](https://www.haorooms.com/post/mysql_1130)
