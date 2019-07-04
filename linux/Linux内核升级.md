# <div style="text-align:center;color:#FF9900">Linux内核升级</div>
参考：[如何在 CentOS 7 中安装或升级最新的内核][]
## CentOS
### 1. ELRepo
##### 1.1 启用 ELRepo 仓库
```sh
$ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
$ rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
```

##### 1.2 查看可安装内核
```sh
$ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
```

##### 1.3 安装内核
```sh
$ yum --enablerepo=elrepo-kernel install kernel-ml
```

### 2. 设置 GRUB 默认的内核版本
打开并编辑 /etc/default/grub 并设置 GRUB_DEFAULT=0。意思是 GRUB 初始化页面的第一个内核将作为默认内核:
> GRUB_TIMEOUT=5
GRUB_DEFAULT=0
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/root rd.lvm.lv=centos/swap crashkernel=auto rhgb quiet"
GRUB_DISABLE_RECOVERY="true"

应用配置：
```sh
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```
### 3. 删除 CentOS 更新后的旧内核
查看系统中全部的内核 RPM 包:
```sh
# rpm -qa | grep kernel

> kernel-tools-3.10.0-514.26.2.el7.x86_64
> kernel-devel-3.10.0-514.10.2.el7.x86_64
> kernel-3.10.0-514.26.2.el7.x86_64
> kernel-3.10.0-327.el7.x86_64
> kernel-ml-4.12.0-1.el7.elrepo.x86_64
> kernel-headers-3.10.0-514.26.2.el7.x86_64
> kernel-devel-3.10.0-514.26.2.el7.x86_64
> kernel-tools-libs-3.10.0-514.26.2.el7.x86_64
```
删除旧内核的 RPM 包
```sh
$ yum remove kernel-tools-3.10.0-514.26.2.el7.x86_64 kernel-devel-3.10.0-514.10.2.el7.x86_64 kernel-3.10.0-514.26.2.el7.x86_64 kernel-3.10.0-327.el7.x86_64 kernel-headers-3.10.0-514.26.2.el7.x86_64 kernel-devel-3.10.0-514.26.2.el7.x86_64 kernel-tools-libs-3.10.0-514.26.2.el7.x86_64
```



<br/><br/>

---

相关网站：
[kernel官网][kernel-offical-website]


[如何在 CentOS 7 中安装或升级最新的内核]: https://linux.cn/article-8310-1.html
[kernel-offical-website]: https://www.kernel.org/
