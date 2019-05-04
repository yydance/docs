本示例在安装前已升级系统内核至5.0
### 安装docker
1.安装docker的repo源
```shell
wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo
```
2.安装启动docker
```shell
yum -y install docker-ce
systemctl start docker
```
### 安装k8s
1.添加k8s repo源

k8s repo:
```
[kubernetes]
name=Kubernetes
baseurl=http://yum.kubernetes.io/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
```
因为某些原因，国内访问受限，替换为阿里源
```
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
        http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
```
2.安装k8s组件
```
yum -y install kubeadm kubectl kubernetes-cni kubelet
```
> k8s依赖包`conntrack`,`ebtables`,`socat`，在epel源，所以安装前先安装epel源
> yum -y install epel-release  

3.查看已安装的k8s版本
```
# kubelet --version
Kubernetes v1.14.1
```
