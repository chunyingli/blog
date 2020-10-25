---
title: Centos8安装K8s
comments: true
mp3: 'http://link.hhtjim.com/163/425570952.mp3'
cover: /img/welcome-cover.jpeg
date: 2020-06-25 23:44:22
updated: 2020-06-25 23:44:22
tags:
    - k8s
categories:
    - k8s
keywords:
    - k8s
    - docker
---
## 1、安装 Docker
### 1. 添加依赖源
``` 
$ curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
```
### 2. 安装Docker
``` 
$ yum -y install docker-ce
```
### 3. 设置Docker开机自启动
``` 
$ systemctl enable --now docker
$ systemctl enable docker.service
```
### 4. 设置阿里云镜像源加速
``` 
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ie2mtbp8.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

## 安装 K8s
### 1. 添加依赖源
``` 
$ cat > /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
EOF
```
### 2. 安装kubeadm
``` 
$ yum install -y kubelet kubeadm kubectl
```
### 3. 设置Kubelet开机自启动
``` 
$ systemctl enable kubelet.service
```
### 4. 下载镜像
在墙外,需要科学上网
``` 
$ kubeadm config images pull
```
### 5. 禁用Swap
``` 
$ vim /etc/fstab
/dev/mapper/cl-swap    >> # /dev/mapper/cl-swap
$ echo vm.swappiness=0 >> /etc/sysctl.conf
```
### 5. Docker设置Systemd
``` 
$  vim /etc/docker/daemon.json
"exec-opts": ["native.cgroupdriver=systemd"]
```
### 5. 初始化
``` 
$ kubeadm init  --pod-network-cidr=10.11.0.0/16
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### 5. 安装 flannel
``` 
$  kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

