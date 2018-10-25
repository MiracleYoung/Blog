---
title: Docker容器基本操作
tags: docker
comments: true
categories:
  - 技术
date: 2018-10-16 17:59:37
---

Docker-镜像管理基础

## Docker Image：
- 采用分层构建机制，最底层为bootfs，其之为rootfs
    - bootfs：用于系统引导的文件系统，包括bootloader和kernel，容器启动完成后会被卸载以节约内存资源
    - rootfs：位于bootfs之上，表现为docker容器的根文件系统

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fwa7wz17k4j30ou0hiq65.jpg)
￼
<!--more-->

最底层的是父镜像，最上层的是“可读写”层，其下均为“只读”层
挂载方式：联合挂载

- 依赖于Aufs，advanced multi-layered unification filesystem，高级多层统一文件系统
- 用于为Linux文件系统实现“联合挂载”
- aufs是UnionFS的重新实现
- Ubuntu上使用的是aufs，CentOS7使用的是devicemapper

## Registry
- Repository
    - 有特定的docker镜像的所有迭代版本组成的镜像仓库
    - 一个Resgistry可以存在多个Repository
        - Repository可分为“顶层仓库”和“用户仓库”
        - 用户仓库名称格式为：用户名/仓库名
    - 每个仓库可以包含多个Tag，每个Tag对应一个镜像
- Index
    - 维护用户账户，镜像的校验以及公共命名空间

## Docker Hub
- Image Repository
- Automated Builds
- Webhooks
- Organizations
- Github and Bitbucket Integration

在Github上写一个DockerFile，DockerHub可以自动监控DockerFile，并创建对应Docker
利用Webhooks去关联Github

## docker pull
docker pull quay.io/flannel:0.10.0-amd64

## 制作镜像
- Dockerfile
- 基于容器制作
- Docker Hub automated builds

### 基于容器制作镜像
- 进入容器进行修改
- 提交新docker的改动：docker commit -p b1, -p pause，暂停container中的状态，b1就是container名字
- 给刚刚提交的容器改动打标签：docker tag <CONTAINER iD> miracle/httpd:v0.1.0
- docker commit -a "Miracle <miracle@qq.com>" -c 'CMD ["/bin/httpd", "-f", "-h", "/data/html/"]' -p b1 miracleyoung/httpd:v0.1
- docker push miracleYoung/httpd, miracleyoung 必须是账号名
- 如果不是推送到DockerHub上去，就要使用：服务器地址/账号名/版本号
- 使用阿里云的Docker，按照阿里云给出的Docker路径登陆

## Docker导入导出
- push 上去，pull下来
- 把已有镜像压缩打包，然后上传到另一台服务器上
    - docker save -o myimages.gz miracleyoung/httpd:v0.1 busybox:latest
    - docker load -I myimages.gz
