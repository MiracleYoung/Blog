---
title: Dockerfile
tags: docker
comments: true
categories:
  - 技术
date: 2018-10-17 23:13:53
---

今天继续输出Docker，等全部看完后，整理成系列。

为了解决自制镜像问题

- 预定义模版，通过设置不同的变量
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwbmml2vqlj30su0b8q4k.jpg)
￼
<!--more-->

## Dockerfile Format
- # 注释
- 指令 参数
    - 指令本身不区分大小写
    - 约定俗成，使用大写
- 第一个指令前必须指定FROM

## Dockerfile 工作流程
- 专用目录存放Dockerfile
- Dockerfile文件名首字母必须大写
- Dockerfile中引入的文件必须在工作目录下
- 使用.dockerignore，引入不需要被打包的东西
- 制作的镜像必须是底层镜像能够提供给我们的环境

## 环境变量
- 引用：$variable_name, ${variable_name}
- ${variable:-word}：给variable设置默认值
- ${variable:+word}：若variable有值则为空字符串，否则是word

## Dockerfile Instructions
- FROM指令是最重的一个，且必须为Dockerfile文件开篇的第一个非注视行，用于为镜像文件构建过程指定基准镜像
- 语法：
    - FROM <repository>[:<tag>] 
    - FROM <repository>@<digest>
        - <repository>: 指定作为base image的名称
        - <tag>: base image的标签，为可选项，可省略
- MAINTAINER(deprecated)
    - 提供本人的详细信息
        - MAINTAINER <author’s detail>
- LABEL
    - 添加一些元数据，可以把作者信息写在LABEL里
        - LABEL <key>=<value>
- COPY
    - 用于从Docker主机复制文件至创建的新镜像文件
        - COPY <src> … <dest>
        - COPY ["<src>", …, "<dest>"]
            - <dest>：建议使用绝对路径，否则将会使用WORKDIR作为起始路径
    - 文件复制准则
        - <src>必须是build上下文中的路径，不能是父路径
        - 如果<src>是目录，则其内部文件或子目录会被递归复制，但<src>目录自身不会被复制
        - 如果指定了多个<src>，或在<src>中使用了通配符，则<dest>必须是一个目录，且以/结尾
        - 若<dest>不存在，则会被自动创建
- Dockerfile中一行就是一层
- 通过dockerbuild来编译Dockerfile，docker build -t tinyhttpd:v0.2 ./
    - -t，指明编译后的镜像tag
- 我们可以通过不用-it进入容器中查询，直接执行一个简单的命令改变默认的shell进入
    - docker run --name tinyweb1 --rm tinyhttpd:v0.1 ls /etc/tmp
- ADD
    - 类似于COPY指令，ADD支持使用TAR和URL路径
    - ADD <src> … <dest>
    - ADD ["<src>", … "<dest>"]
    - 操作准则
        - 同COPY指令
            - 如果<src>为URL且<dest>不以/结尾，则<src>指定的文件将被下载并直接被创建为<dest>
            - 若<dest>以/结尾，则被下载并保存为<dest>/<filename>
            - 若<src>是一个本地系统上的压缩格式的tar文件，则会被展开为一个目录，若<src>是个URL上的tar文件，则不会被展开，只会保存
- WORKDIR
    - 用于为Dockerfile指定工作目录，可以指定多个
    - WORKDIR <镜像中的目录>
- VOLUME
    - 用于在image中创建一个挂在点目录
    - VOLUME <mountpoint>
    - VOLUME ["<mountpoint>"]
    - 如果挂载点目录路径下此前有文件存在，docker run 命令会在卷挂载完毕后将此前的所有文件复制到新挂载的卷中。
- EXPOSE
    - 用于为容器打开指定要监听的端口以实现与外部通信
    - EXPOSE <port>[/<protocol>][<port>[/protocol]…]
        - protocol: tcp/udp 默认tcp
    - 可一次指定多个端口：EXPOSE 11211/udp 11211/tcp
    - 需要注意的是，这个EXPOSE中暴露的端口是可以暴露的端口，但是最重需要docker run -p 来暴露，也可以使用-P来把所有的EXPOSE都暴露
    - docker port tinyweb1 查看端口映射
- ENV
    - 用于为镜像所需要的环境变量，并可被Dockerfile文件中位于其后的其他指令所调用
    - ENV <key> <value>
    - ENV <key>=<value>…
    - docker run 的时候可以向container传值，但是这样会影响到容器本身的变量
￼![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwbmmx7yatj30tm08ewgc.jpg)
    
    - 可以在docker run 启动的时候指定环境变量
        - docker run --name tinyweb1 --rm --env WEB_SERVER_PACKAGE=nginx1.15.1 tinyhttpd:v0.7 printenv
￼![](https://ws3.sinaimg.cn/large/006tNbRwgy1fwbmnll17dj30q8080gmj.jpg)

但并不会影响build，因为run在build后面

- RUN
    - 用于指定docker build过程中运行的程序
    - 发生在build的过程中
    - RUN <command>
    - RUN ["<executable>", "<param1>", "<param2>"]
        - 但这样子的指令集不是以shell 启动 /bin/sh -c
        - 所以可以替换成 RUN ["/bin/sh", "-c", "<executable>", "<param1>"]
- CMD
    - 如果没有指定run，默认运行CMD
    - 发生在run的过程中
    - Dockerfile中可以存在多个CMD指令，但仅最后一个会生效
    - CMD <command>
    - CMD ["<executable>", "<param1>", "<param2>"]
        - 但这样子的指令集不是以shell 启动 /bin/sh -c
        - 所以可以替换成 CMD ["/bin/sh", "-c", "<executable>", "<param1>"]
