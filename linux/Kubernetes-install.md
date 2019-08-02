# <div style="text-align:center;color:#FF9900">Kubernetes 安装</div>
## 1. kubeadm方式
参考：[2019最新k8s集群搭建教程(centos k8s 搭建)][]
#### 1.0 准备
1. 节点之中不可以有重复的主机名；修改主机名的方式请参考[三种方法实现对CentOS7主机名的修改][]，同时也要配置host映射文件`/etc/hosts`；
2. 检查所需端口：
    ```sh
    lsof -t -i :6443
    lsof -t -i :10250
    lsof -t -i :10251
    lsof -t -i :10252
    lsof -t -i :2379
    lsof -t -i :2380
    ```

#### 1.1 卸载原docker
```sh
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

#### 1.2 安装docker
```sh
# 安装依赖
$ sudo yum update -y && sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

# 添加官方yum库
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# 安装docker
$ sudo yum install docker-ce docker-ce-cli containerd.io

# 查看docker版本，操作本篇博客时使用的docker是18.09.6版本
$ docker --version

# 开机启动
systemctl enable --now docker
```
##### 1.2.1 docker命令自动补全
1. 安装`bash-completion`
    ```sh
    $ yum -y install bash-completion
    ```

2. 执行`/usr/share/bash-completion/bash_completion`文件
    ```sh
    $ sh /usr/share/bash-completion/bash_completion
    ```

3. 重启终端

#### 1.3 修改docker cgroup
修改docker cgroup驱动，与k8s一致，使用systemd
```sh
# 修改docker cgroup驱动：native.cgroupdriver=systemd
$ cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

$ systemctl daemon-reload && systemctl restart docker  # 重启使配置生效
```
验证：
```sh
$ docker info | grep 'Cgroup Driver'
```

##### 1.3.1 修改kubelet的cgroup
只要docker和kubelet的cgroup统一即可，所以也可以修改kubelet的cgroup，具体步骤是：

1. 修改配置文件：/usr/lib/systemd/system/kubelet.service.d/10-kubeadm.conf，设置cgroup为cgroupfs
    ```sh
    # Note: This dropin only works with kubeadm and kubelet v1.11+
    [Service]
    Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
    Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
    Environment="KUBELET_CGROUP_ARGS=--cgroup-driver=cgroupfs"
    # This is a file that "kubeadm init" and "kubeadm join" generates at runtime, populating the KUBELET_KUBEADM_ARGS variable dynamically
    EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
    # This is a file that the user can use for overrides of the kubelet args as a last resort. Preferably, the user should use
    # the .NodeRegistration.KubeletExtraArgs object in the configuration files instead. KUBELET_EXTRA_ARGS should be sourced from this file.
    EnvironmentFile=-/etc/sysconfig/kubelet
    ExecStart=
    ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS
    ```
2. 重启服务
    ```sh
    $ systemctl daemon-reload && systemctl restart kubelet  # 重启使配置生效
    ```


#### 1.4 安装 kubelet kubeadm kubectl
master、node节点都需要安装kubelet kubeadm kubectl。

安装kubernetes的时候，需要安装kubelet, kubeadm等包，但k8s官网给的yum源是packages.cloud.google.com，国内访问不了，此时我们可以使用阿里云的yum仓库镜像
```sh
# 设置kubernetes仓库
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 关闭SElinux
$ setenforce 0
$ sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# 安装kubelet kubeadm kubectl
$ yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

$ systemctl enable --now kubelet  # 开机启动kubelet，请注意下面“说明”

# centos7用户还需要设置路由：
$ yum install -y bridge-utils.x86_64
$ modprobe  br_netfilter  # 加载br_netfilter模块，使用lsmod查看开启的模块
$ cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
$ sysctl --system  # 重新加载所有配置文件

$ systemctl disable --now firewalld  # 关闭防火墙

# k8s要求关闭swap  (qxl)
$ swapoff -a && sysctl -w vm.swappiness=0  # 关闭swap
$ sed -ri '/^[^#]*swap/s@^@#@' /etc/fstab  # 取消开机挂载swap
```
**说明**：上面步骤中`systemctl enable --now kubelet`会启动失败，但是不要担心，相关解释可参考[kubelet服务启动失败，错误代码255][]

#### 1.5 创建集群前准备
**master**:
```sh
# 拉取集群所需镜像，这个需要翻墙
$ kubeadm config images pull

# --- 不能翻墙可以尝试以下办法 ---
$ kubeadm config images list # 列出所需镜像

#(不是一定是下面的,根据实际情况来)
# 根据所需镜像名字先拉取国内资源
docker pull mirrorgooglecontainers/kube-apiserver:v1.15.0
docker pull mirrorgooglecontainers/kube-controller-manager:v1.15.0
docker pull mirrorgooglecontainers/kube-scheduler:v1.15.0
docker pull mirrorgooglecontainers/kube-proxy:v1.15.0
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/etcd:3.3.10
docker pull coredns/coredns:1.3.1  # 这个在mirrorgooglecontainers中没有

# 修改镜像tag
docker tag mirrorgooglecontainers/kube-apiserver:v1.15.0 k8s.gcr.io/kube-apiserver:v1.15.0
docker tag mirrorgooglecontainers/kube-controller-manager:v1.15.0 k8s.gcr.io/kube-controller-manager:v1.15.0
docker tag mirrorgooglecontainers/kube-scheduler:v1.15.0 k8s.gcr.io/kube-scheduler:v1.15.0
docker tag mirrorgooglecontainers/kube-proxy:v1.15.0 k8s.gcr.io/kube-proxy:v1.15.0
docker tag mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag mirrorgooglecontainers/etcd:3.3.10 k8s.gcr.io/etcd:3.3.10
docker tag coredns/coredns:1.3.1 k8s.gcr.io/coredns:1.3.1
```
把所需的镜像下载好，init的时候就不会再拉镜像，避免由于无法连接google镜像库而导致出错
```sh
# 删除原来的镜像
docker rmi mirrorgooglecontainers/kube-apiserver:v1.15.0
docker rmi mirrorgooglecontainers/kube-controller-manager:v1.15.0
docker rmi mirrorgooglecontainers/kube-scheduler:v1.15.0
docker rmi mirrorgooglecontainers/kube-proxy:v1.15.0
docker rmi mirrorgooglecontainers/pause:3.1
docker rmi mirrorgooglecontainers/etcd:3.3.10
docker rmi coredns/coredns:1.3.1
```
**Node端**:
```sh
# 根据所需镜像名字先拉取国内资源
docker pull mirrorgooglecontainers/kube-proxy:v1.15.0
docker pull mirrorgooglecontainers/pause:3.1

# 修改镜像tag
docker tag mirrorgooglecontainers/kube-proxy:v1.15.0 k8s.gcr.io/kube-proxy:v1.15.0
docker tag mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1

# 删除原来的镜像
docker rmi mirrorgooglecontainers/kube-proxy:v1.15.0
docker rmi mirrorgooglecontainers/pause:3.1
```

#### 1.6 创建集群
```sh
# 初始化Master（Master需要至少2核）此处会各种报错,异常...成功与否就在此
$ kubeadm init --apiserver-advertise-address <master机器的ip> --pod-network-cidr 10.244.0.0/16 --kubernetes-version 1.15.0

# 比如：
$ kubeadm init --apiserver-advertise-address 192.168.3.133 --pod-network-cidr 10.244.0.0/16 --kubernetes-version 1.15.0
```
> --apiserver-advertise-address 指定与其它节点通信的接口
--pod-network-cidr 指定pod网络子网，使用fannel网络必须使用这个CIDR

##### 1.6.1 Token
参考[kubeadm token][]
```sh
# 相关命令
$ kubeadm token create
$ kubeadm token delete
$ kubeadm token generate
$ kubeadm token list
```

#### 1.7 应用flannel网络
参考[pod-network][]
```sh
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
# 或
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/62e44c867a2846fefb68bd5f178daf4da3095ccb/Documentation/kube-flannel.yml
```
#### 1.8 安装Dashboard
参考[安装 Dashboard 插件][]

#### 1.9 More
> kubeadm还处于 beta 状态，还不能用于生产环境，对于现阶段想要用于生产环境的，建议还是参考[手动搭建高可用的kubernetes 集群][]

[2019最新k8s集群搭建教程(centos k8s 搭建)]: https://juejin.im/post/5cb7dde9f265da034d2a0dba
[kubelet服务启动失败，错误代码255]:https://blog.csdn.net/nklinsirui/article/details/80583971
[三种方法实现对CentOS7主机名的修改]:https://blog.csdn.net/solaraceboy/article/details/78563537
[kubeadm token]:https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-token/
[pod-network]:https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network
[安装 Dashboard 插件]:https://www.qikqiak.com/k8s-book/docs/17.%E5%AE%89%E8%A3%85%20Dashboard%20%E6%8F%92%E4%BB%B6.html
[手动搭建高可用的kubernetes 集群]:https://www.qikqiak.com/post/manual-install-high-available-kubernetes-cluster/
