---
title: k8s-3-初始化k8s集群
tags: k8s
comments: true
categories:
  - 技术
date: 2018-10-26 06:01:33
---

k8s-3-初始化k8s集群
￼
￼![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwl7bytqsnj30sy0ma426.jpg)
<!--more-->
- 手动安装，部署在系统之上
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fwl7c4fe6qj30rs0eigo1.jpg)
- kubeadm：把k8s自己也部署成容器
    - kubelet：运行pod的基础组件
    - docker：运行docker
- master上运行：API Server、etcd、controller-manager、scheduler，都运行为Pod的方式
- 各组件都需要flannel
- master，nodes：安装kubelet，kubeadm，docker
- master：kubeadm initi
- nodes：kubeadm join

- kubelet, kubeadm, kuernetes 安装
    - yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    - wget -P /etc/yum.repos.d/ https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg 
```
[root@centos7-1 yum.repos.d]# cat kubernetes.repo
[kubernetes]
name=Kunernetes Repo
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
enabled=1
```
    - wget https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
    - yum install kubelet kubeadm kubectl
- vim /usr/lib/systemd/system/docker.service
    - Environment="HTTPS_PROXY=http://www.ik8s.io:10080”
    - Environment=“NO_PROXY=127.0.0.0/8,172.17.0.0/16"
- systemctl daemon-reload
- systemctl restart docker 
- cat /proc/sys/net/bridge/bridge-nf-call-ip6tables 确认是1
- cat /proc/sys/net/bridge/bridge-nf-call-iptables 确认是1

- 初始化k8s：kubeadm init --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap
- run cmd：
    - mkdir -p $HOME/.kube
    - sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    - sudo chown $(id -u):$(id -g) $HOME/.kube/config
- 加入其他节点，join nodes
    - kubeadm join 172.16.163.129:6443 --token qzkzzn.kcros8j24l7adsi5 --discovery-token-ca-cert-hash sha256:84de83f916b04644f38f893246b9daf064967871ae3114726df2ea8fad63e7d8 --ignore-preflight-errors=Swap
- 修改配置文件：/etc/sysconfig/kubelet
    - KUBELET_EXTRA_ARGS="--fail-swap-on=false"

- kubectl get cs
- kubectl get nodes


- 部署flannel
    - kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    - 通过kubectl get pods -n kube-system 查看pod 情况
    - kubectl get ns，查看kube的命名空间，一共有3个，default，kube-public，kube-system

- 从节点部署，只需要kubeadm, kubelet, docker-ce
