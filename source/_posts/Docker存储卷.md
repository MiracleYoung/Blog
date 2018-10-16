---
title: Docker存储卷
tags: 技术
comments: true
categories:
  - 技术
date: 2018-10-16 18:02:11
---

Docker-存储卷

- Docker镜像由多个只读层叠加而成，启动容器时，Docker会加载只读镜像层并在镜像栈顶部添加一个读写层
- 将容器的文件系统和物理机的文件系统绑定关联，容器的数据存储在宿主机的存储目录上，实现数据生命永久
￼![](https://ws2.sinaimg.cn/large/006tNbRwgy1fwa7zou50oj30qu0iqmz7.jpg)
<!--more-->

- 集群文件系统
￼![](https://ws1.sinaimg.cn/large/006tNbRwgy1fwa819mgtjj30uq0li76u.jpg)

- 卷为Docker提供了独立于容器的数据管理机制
￼![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwa81fttovj30xw0f4mzl.jpg)

- 有两种类型的卷：
    - bind mount volume,指定宿主机的路径和docker的路径
    - Docker-managed volume，依托docker自主管理
￼![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwa81kxnu2j30xa0ceac4.jpg)

- 在容器中使用Volumes
    - Docker-managed volume: docker run --name bbox1 --it -v /data/ busybody
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fwa81uyysvj30tg098t9v.jpg)
￼![](https://ws3.sinaimg.cn/large/006tNbRwgy1fwa81ydvc0j30ok0om771.jpg)
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwa820f69qj31js0ggdic.jpg)
￼
    - Bind-mount volume: docker run -it -v HOSTDIR:VOLUMEDIR --name bbox2 busybody
        - docker run --name b2 -it --rm -v ~/data/b2:/data busybox
            - 需要注意的是，Mac下的共享目录有限制，默认是/Usr, /Volumes, /tmp, /private
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fwa82dhu4xj30tk05w0tg.jpg)
￼![](https://ws2.sinaimg.cn/large/006tNbRwgy1fwa82ekarrj30ek056q3a.jpg)

- 如果多个容器挂在同一个目录，那么将共享文件
- 也可以在启动容器的时候指定，我要使用之前的哪个容器的卷
    -  --volumes-from b2 ，指定已经存在的容器名

为了让Nginx、Tomcat、MySQL都能够同步在同一个网断下，共享同一个存储卷，还需要指定网络：
- docker run --name infravol --it -v /data/infravol/volumes:/data/web/html busybox
- docker run --name web1 --network container:infravol --volumes-from infravol -it busybox
