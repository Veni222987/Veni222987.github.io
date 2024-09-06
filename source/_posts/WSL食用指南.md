---
title: WSL食用指南
cover_picture: https://img1.baidu.com/it/u=751563930,437961120&fm=253&fmt=auto&app=138&f=JPEG?w=600&h=338
author: Veni
categories: tools
tags:
  - 工具
date: 2023-12-15 23:25:19
---

这是一个WSL开发的使用指南，可以享受Linux的开发环境和Windows的办公环境（主要是买不起Mac）。下面是使用WSL开发的基本指南。<!--more-->

# WSL食用指南

## 基础配置篇

CentOS已经停止维护，推荐使用Debian，轻量且稳定。

要想使用Debian开发，需要完成下面几件事情：

1. 更换apt源，阿里源或者清华源都可（如果是云服务器则不需要手动换源，一般对应厂商已经换好）。
2. apt install build-essentials，各种开发的基础。



换源教程：

- 安装Debian 12.0(bookworm)，执行`apt update`更新apt。

- 下载https支持：

```shell
apt install apt-transport-https ca-certificates
```

- 修改清华源：

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib

deb https://security.debian.org/debian-security bookworm-security main contrib
# deb-src https://security.debian.org/debian-security bookworm-security main contrib
```

- 再次执行`apt update`
- 下载必要的编译环境，这是各类开发包的基础，如不安装，则无法安装其他部分SDK。

```shell
apt install build-essential
```

之后就可以按需安装需要的软件了

## 开发篇

### 网络调试

使用WSL开发后端，进行接口调试的时候可以有多种方式。

#### IDE端口转发

一种是直接使用IDEA(或其他IDE的类似功能)的端口转发。如下图所示：将2345端口转发到主机的13151端口，之后调用localhost:13151端口访问。每次启动都会改变端口，推荐前端使用此方法调用，例如开发Vue等项目需要运行的时候，可以使用本机浏览器测试。

![image-20231215233103387](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312152331769.webp)

#### 使用netsh interface portproxy转发

另外一种是放行WSL的防火墙，进行接口调试。这种方法设置的ip长期有效，端口固定，在使用APIFOX的时候不用每次都修改端口号。推荐后端开发使用此方法。步骤如下：

查看WSL的ip地址：`ip address`：

![image-20231215234033137](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312152340280.webp)

修改Windows网络转发规则：

```cmd
netsh interface portproxy add v4tov4 listenport=5354 listenaddress=0.0.0.0 connectport=5354 connectaddress=172.18.157.129
```

`listenport`, 表示要监听的 Windows 端口
`listenaddress`, 表示监听地址, 0.0.0.0 表示匹配所有地址, 比如Windows 既有Wifi网卡, 又有有线网卡, 那么访问任意两个网卡, 都会被监听到,当然也可以指定其中之一的IP的地址
`connectaddress` ,要转发的地址, 这里设置为localhost, 是因为,我们可以通过localhost来访问WSL2, 如果暂不支持, 这里需要指定为 WSL2的IP地址
`connectport`, 要转发到的目标端口，即虚拟机监听的端口

> 推荐转发的端口以2xxxx命名，与To同音，例如28080, 28088, 26379

附netsh的常用指令：

查看端口转发：

```cmd
netsh interface portproxy show all
```

删除端口转发：
```cmd
netsh interface portproxy delete v4tov4 listenport=5354 listenaddress=0.0.0.0
```

这个时候，对于本机而言，使用127.0.0.1:5354访问和使用172.18.157.129:5354访问的结果是一样的，都可以正常访问WSL中启动的后端项目。

- 局域网访问：使用防火墙配置入站规则，放行对应端口后，重启电脑即可。

![image-20231216002903288](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312160029651.webp)

#### 使用容器转发

使用容器,来配置web服务,显然是最佳选择,这个时候你就会遇到本文的问题,因为你可能希望让其他主机来访问你的容器服务!但是还未实践，此处略过。
