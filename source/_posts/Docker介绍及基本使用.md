---
title: Docker介绍及基本使用
tags: 技术
comments: true
categories:
  - 技术
date: 2018-10-11 23:03:46
---

很久没写博客了，真的不好，今天就当是一个新的开始。

终于有时间可以静下心来完整的看一遍Docker了，之前都是自己瞎玩，感觉差的不是一点两点，今天看了马哥的Docker容器第一章和第二章收获确实颇多。

<!--more-->

## Docker的概念

Docker也是一个虚拟化的技术，相较于传统的VirtualMachine，区别是VMs是需要一个完整的操作系统，无论你要运行单一的Nginx又或者是多个MySQL实例，你都需要先安装一个虚拟操作系统，然后在其之上搭建服务。另外，如果你需要多个服务进行隔离，互不干扰，那么就需要使用到隔离技术，隔离技术早就有了，不过这并不是Docker。
那么Docker是什么呢？Docker是一个把服务单一化，独立于操作系统的虚拟化技术。举个例子就能理解了：如果我要在Docker里运行Nginx，那么这个Docker里就只跑了一个Nginx，是只有一个Nginx，连PID=1 的那个init都没有，取而代之的是PID=1的Nginx服务。

## Docker的组件

Docker的组件有很多，不过今天就学了3种：Container（容器）、Image（镜像）、Registry（仓库）。

什么区别呢？一个个说。

仓库存储着大量的镜像，每一个镜像都是一个类，而容器就是一个个具体的实例，可以理解为是实例化了。就好像你安装了MySQL，但没有运行MySQL实例，这个时候服务是不存在的，这就是镜像，镜像只是一个静态的东西，需要你去实例化镜像，实例化出来的东西就叫容器。

仓库里有许多的镜像，例如：Nginx、MySQL等。一个Nginx服务包含很多个版本，相同的版本还有stable、release、alpine等，所以一个仓库下包含着许多的镜像。
为了区分他们，命名规则上做出了规范：从仓库中获取一个具体的镜像，需要这么称呼他：`nginx:1.14-alpine`，这样就能找到唯一的镜像了。

## Docker的基本操作

Docker发展到现在，已经相当不错了，在命令行里也已经组件化了，我是Mac，所以直接安装的Docker for Mac。安装完后就可以直接使用了。

- `docker pull nginx:1.14-alpine`：从仓库中拉取一个nginx，版本号是1.14-alpine
- `docker run --name web1 -d nginx:1.14-alpine`： -d 参数代表使用daemon的方式启动docker，--name 表示指定该container的名字，最后的nginx:1.14-alpine代表这个容器所使用的镜像名字。
我们可以通过 `docker container ps` 来查看当前docker的运行状况
![docker container ps](https://ws2.sinaimg.cn/large/006tNbRwgy1fw4p8ohd7qj31kw09an0o.jpg)
- `docker exec -it web1 /bin/sh`：exec 表示进入docker container里面去，-it的意思就是交互式操作，web1就是进入到名字叫web1的container里去，最后的/bin/sh 就是以什么样的方式进入，如果你是一个echo，就直接输出了
![docker exec -it web1 /bin/sh](https://ws4.sinaimg.cn/large/006tNbRwgy1fw4pbpiwznj31460i8wgz.jpg)
![docker exec -it web1 echo 1](https://ws2.sinaimg.cn/large/006tNbRwgy1fw4p8ohd7qj31kw09an0o.jpg)

但是在这里会有一个坑，也是我希望记录下来的，在Mac中，1024端口以下是需要sudo权限的，而linux没有，所以在视频中，启动了nginx后，是可以在外部使用内网ip:port的方式访问到这个nginx的，而我却不行，之后查阅文档，发现run是有一个端口映射参数的，加上去就好了。
`docker run -p 8080:80 --name web1 -d nginx:1.14-alpine`：这里，把container中的80端口映射到我本地的8080端口。

通过命令`docker inspect web1` 查看web1容器的详细情况：
![docker inspect web1](https://ws1.sinaimg.cn/large/006tNbRwgy1fw4plrxnukj30w40h0myr.jpg)
![docker inspect web1](https://ws4.sinaimg.cn/large/006tNbRwgy1fw4pltr77aj314s0xatda.jpg)

这样子，就可以本地访问到了。
![docker run -p 8080:80 --name web1 -d nginx:1.14-alpine](https://ws1.sinaimg.cn/large/006tNbRwgy1fw4plv5oldj30rm10ogq8.jpg)