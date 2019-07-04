# <div style="text-align:center;color:#FF9900">Kubernetes</div>
```sh
kubeadm init --apiserver-advertise-address 192.168.3.131 --pod-network-cidr 10.244.0.0/16 --kubernetes-version 1.15.0
```

### & 其他
#### &1. 命令自动补全
* kubectl:
   ```sh
   $ source <(kubectl completion bash)  # 临时修改
   $ echo "source <(kubectl completion bash)" >> ~/.bashrc  # 永久
   ```
* 优雅地终止容器
默认所有的优雅退出时间都在30秒内。kubectl delete 命令支持` --grace-period=<seconds>`选项，参考[Pod Hook][]
  > 值'0'代表 强制删除 pod. 在 kubectl 1.5 及以上的版本里，执行强制删除时必须同时指定 --force --grace-period=0

  强制删除并不是立即终止容器：
  > 强制删除一个 pod 是从集群状态还有 etcd 里立刻删除这个 pod。 当 Pod 被强制删除时， api 服务器不会等待来自 Pod 所在节点上的 kubelet 的确认信息：pod 已经被终止。在 API 里 pod 会被立刻删除，在节点上， pods 被设置成立刻终止后，在强行杀掉前还会有一个很小的宽限期

#### &2. 常用命令
```sh
$ kubectl get pods --all-namespaces

# 创建命名空间
$ kubectl create namespace blog

# HPA
$ kubectl autoscale deployment your-deployment --cpu-percent=10 --min=1 --max=10 -n namespace
```









[Pod Hook]:https://www.qikqiak.com/k8s-book/docs/20.Pod%20Hook.html
