---
title: Docker部署项目指南
tags:
  - Docker
categories: Docker
cover: https://th.bing.com/th/id/R.5c0f421f5c50f404c33140eeedf9edb2?rik=OKr%2fSjuxEKty4w&pid=ImgRaw&r=0
highlight_shrink: false
date: 2024-01-24 13:41:18
---

# Docker简介

这篇文章是新手向的Docker使用文章。所以现在介绍一下Docker吧。根据Docker官方的说法：

> Docker helps developers build, share, run, and verify applications anywhere — without tedious environment configuration or management.

简单来说就是一个帮助开发者构建、运行和分享应用的容器，它可以让你无需考虑在不同的机器上运行的时候的环境配置问题。想象一下你自己写好的一个软件，但是到了别人的计算机无法运行，又找不到环境配置哪里没配好，那个情景有多难受。这时候，如果使用了Docker，你就会明白什么叫纵享丝滑部署。

## Docker安装

这里的Docker安装指的是安装Docker-CE(Community Edition)。根据[Docker官方指导](https://docs.docker.com/engine/install/)，有四种安装方法，这里介绍两种。

- **Docker Desktop**

主要适用于带有桌面的计算机，如Windows，Ubuntu桌面等。安装了DockerDesktop就相当于安装了Docker-CE, Docker-CLI等一系列工具。对于WSL，只需要在Windows主系统安装DockerDesktop并勾选适用WSL。

- **安装包安装(apt、yum等)**

大陆用户推荐使用阿里云镜像安装，根据[指导](https://developer.aliyun.com/mirror/docker-ce)执行对应的shell脚本即可。

如果安装过程出现了任何问题，都可以阅读上面的官方指导链接里面的内容寻找解决方法。

## Docker基本使用

先搞清楚Docker的三个概念：镜像、容器和进程：

- **镜像（Image）：**Docker镜像是一个只读的模板，它包含了运行容器所需的所有文件系统、应用程序代码、依赖项和配置信息。镜像是用于创建Docker容器的基础。

查看镜像：

```bash
docker images
```

删除镜像：

```bash
docker rmi <镜像名或id>
```

创建镜像后面详细说。

- **容器（Container）：**Docker容器是从Docker镜像创建的运行实例。容器是独立且轻量级的，它包含了运行应用程序所需的所有内容，包括文件系统、代码、依赖项和配置。这一层的操作是最重要的，只有熟悉容器的操作，才能用好Docker。

查看容器：

```bash
docker ps
```

查看容器端口映射：

```bash 
docker port <容器名>
```

从镜像创建容器并运行：

```bash
docker run <镜像名>
# 运行时指定端口映射
docker run -p <主机端口>:<容器端口> <镜像名称>
```

删除容器：

```bash
docker rm <容器名字或id>
```

重启容器：

```bash
docker restart <容器名或id>
```

进入容器内部：

```bash
docker exec -it <容器ID或名称> /bin/bash
```

- **进程（Process）：**在Docker容器中运行的应用程序被视为一个或多个进程。容器内部的进程与宿主机的进程隔离，它们在自己的命名空间中运行，并且只能访问容器内部的资源。

# 部署Docker镜像

## 部署Docker Hub镜像

Docker Hub是一个面向Docker开发者和用户的公共注册表服务。它是一个集中存储、分享和管理Docker镜像的平台。我们可以从Docker Hub中拉取镜像，也可以上传自己的创建的镜像。

下面将以部署Redis为例，讲一下如何部署他人已经上传的镜像。上文说到过，要创建一个容器必须要有镜像，所以第一步就是将容器镜像从docker hub上拉下来：

```bash
docker pull redis
```

具体的指令一般都会在镜像对应页面的右上角：

![image-20240224143645281](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/image-20240224143645281.png)

执行完成之后，可以用`docker images`指令查看本地镜像。如果有对应的镜像，那就可以run了。一般来说，Docker hub上有对应的启动和配置教程，细心阅读即可。按照默认运行，设置端口映射可以执行如下指令：

```
docker run --name redis -p 6379:6379 -d redis
```

然后ps一下看看有没有成功运行就可以了。总的来说，部署他人的容器镜像比较简单，耐心阅读指导就可以了。

## 自己创建镜像部署

自己创建镜像需要编写Dockerfile，定义和描述如何创建Docker镜像。例如，创建一个Spring boot镜像的Dockerfile可以写成如下形式：

```dockerfile
FROM openjdk:17
WORKDIR /vshop
COPY . /vshop
EXPOSE 8080
CMD ["java","-jar","/vshop/vshop.jar",">>","/vshop/log.log","&"]
```

下面是Dockerfile中的一些常见配置：

- FROM：定义基础镜像，确定构建的起点
- WORKDIR：容器中的工作目录
- RUN、ADD、COPY：安装软件包，依赖项和文件到镜像中

- EXPOSE：配置端口

- ENV：配置容器中的环境变量
- CMD：启动容器时执行的指令

编写完Dockerfile之后，将Dockerfile和打包完成的jar包放在新建目录下，然后执行如下指令：

```bash
docker build -t vshop .
```

-t指定了打包的名字和标签，. 表示在当前目录下寻找Dockerfile并且打包。打包完成之后，使用`docker images`查看镜像即可。上面的案例仅作一个简单的示例，事实上Dockerfile大部分都是不通用的，例如将Spring boot项目打包成为war包然后运行在Tomcat容器中，则需要进行两层构建，第一层基于Tomcat构建基础镜像，第二层构建最终的镜像。当需要打包不同类型的项目的时候，可以查阅官方文档或者自行Google一下。

# 发布Docker镜像

前面介绍过Docker Hub了，现在讲讲如何将自己构建的Docker项目发布到Docker Hub上。首先需要自己注册一个Docker账号并且执行一下`docker login`登录Docker。然后的操作就和Git推送到GitHub差不多。

使用`docker tag`给镜像添加标签：

```bash'
docker tag <镜像ID> <用户名>/<仓库名>:<版本号>
```

然后直接push到Docker Hub即可：

```bash
docker push <用户名>/<仓库名>:<版本号>
```

当push完成以后，在另外一台电脑（如服务器）上运行这个项目就回到了部署那一部分的内容了。现在你已经掌握了Docker构建和部署项目的基本流程了。
