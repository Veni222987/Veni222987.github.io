---
title: 【Sangfor】数据中心与云计算
cover_picture: https://img0.baidu.com/it/u=1695559041,492878697&fm=253&fmt=auto&app=120&f=JPEG?w=570&h=342
author: Veni
categories: notes
tags:
  - 云计算
date: 2023-10-16 17:23:04
---

本文是深信服云计算学习课程的笔记，主要记录了数据中心、负载均衡的相关重点知识。<!--more-->

# 数据中心与云计算

## 数据中心

### 数据中心概述

数据中心（DC）是为集中放置的电子信息设备提供运行环境的建筑场所，包括主机房、辅助区等等，也就是一个实现信息的集中处理、存储、传输交换的物理空间。

互联网数据中心（IDC）包括了高速的接入带宽、高性能局域网等等。

#### 云数据中心

> 2006年亚马逊推出云数据中心，开创了云数据中心的时代。我国在2007年底开始使用云服务。

传统的IT架构：
<div style="text-align:center">
    <img src="https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161738200.webp" alt="image" />
</div>

传统的数据中心缺点：资源分布分散，利用率低，平均业务恢复时间长，手工分配资源，资源无法弹性适配，多DC分散管理，协同性差。于是云数据中心成为了主流。

![image-20231016174921857](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161749114.webp)

云数据中心优点：虚拟化、安全可靠、敏捷性高。

### 数据中心和服务器

####  什么是服务器？

服务器是计算机的一种，指在网络环境下运行特定的软件，为客户提供服务的计算机。服务器的特性：高可用性、可靠性、可扩展性、易用性、易管理性。

服务器应用部署的常见架构：

![image-20231016175247891](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161752124.webp)

#### 服务器的常见软件：

![image-20231016175458410](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161754636.webp)

特殊系统软件：openstack虚拟化软件

**服务器虚拟化**：将物理资源抽象成为逻辑资源：将一台服务器变成几台甚至上百台相互隔离的虚拟服务器。

服务器的常见处理器架构：
![image-20231016180057517](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161800839.webp)

安装软件的时候需要注意架构，鲲鹏、ARM和x86、AMD64一般有区别。

#### 服务器总体层次

下图总结了服务器的总体层次架构：

![image-20231016180432518](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161804776.webp)

#### 服务器的硬件结构

服务器的硬件结构与一般的计算机结构基本一致，此处只摘取重点记录。

服务器内存条的注意事项：
![image-20231016180838403](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161808782.webp)

硬盘的接口性能示意图：
![image-20231016181032036](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161810386.webp)

所以买硬盘优先选择PCIe接口，最好不要选择SATA接口。

**RAID技术（服务器关键技术）**：有多个独立的高性能磁盘驱动器组成的磁盘子系统，提供比单个磁盘更高的性能。实现方式有两种：

- 硬件RAID：使用硬件RAID卡
- 软件RAID：通过操作系统在磁盘管理的方式管理磁盘组

BMC：BMC是基础管理控制器（Baseboard Management Controller）的缩写。它是一种嵌入式硬件设备，位于服务器或计算机系统的主板上，用于远程管理和监控系统的运行。BMC是一个独立的系统，不依赖其他硬件，也不依赖BIOS，OS等等，但可以与之交互。

### 负载均衡

#### 负载均衡概述

负载均衡是一种在计算机网络中分配工作负载的技术，通过将请求分发到多个服务器上，以平衡服务器之间的负载，从而实现更高的吞吐量和更快的响应时间。

下面是一个负载均衡的示例：假设有一个负载均衡器和三台服务器（Server A、Server B和Server C）。当客户端发送请求时，请求首先到达负载均衡器。然后，负载均衡器根据预定义的算法（例如轮询、最小连接数等）选择一个服务器，并将请求转发到该服务器。

负载均衡分类：
**四层负载均衡（TCP）**：流量分配到**ip+port**，一般用**LVS**实现

**七层负载均衡（http）**：流量分配到不同的**url**，一般用**Nginx**实现

#### 负载均衡实现案例：DNS轮询

多个服务器分配不同的IP地址，每次发过来的流量轮流发到不同的服务器中。

工作原理如下：

- **分配域名：**在DNS服务器的配置中，为同一个域名（例如example.com）配置多个A记录，每个A记录对应一个服务器的IP地址。
- **轮询IP：**当客户端发送一个DNS查询请求以获取域名的IP地址时，DNS服务器会按照预定义的顺序返回这些IP地址。
- **连接服务器：**客户端将使用返回的IP地址之一，发起与服务器的连接。
- **下次选择：**下一个客户端请求会按照相同的顺序，再次选择下一个IP地址。

#### 负载均衡方案LVS

##### LVS基础知识

LVS全称Linux virtual server的简称。LVS服务器集群一般采用三层结构：

- **负载调度器：**整个集群对外前端机，负责将客户的请求发到一组服务器执行，而客户认为服务来自同一个IP地址。
- **服务器池：**一组真正执行客户请求的服务器，如WEB，MAIL，FTP和DNS服务器等。
- **共享存储：**为服务器池提供共享的存储区

##### LVS部署模式：三种

LVS相关的几种IP：CIP, VIP, DIP, RIP

![image-20231016184603525](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161846727.webp)

###### NAT模式：

通过数据报头的修改，使得企业内部的私有IP地址可以访问外网，以及外部用户可以访问公司内网的私有IP主机。NAT有一个哈希表记录映射关系，用于实现流量通信的匹配。

访问WEB服务器的VIP->从调度列表中选出一台服务器，修改报头为DIP->访问RIP，从服务器返回实际的报文->建立通信，多次发送。

![image-20231016185245577](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161852789.webp)

NAT模式的基本属性：

1. RIP和DIP处于同一私有网段中（如无法满足，则确保它们能通信）
2. 各RealServer指向DIP，保证响应能够交给Director。
3. 缺点是Director负责所有进出数据，而响应数据比请求数据大得多，调度器容易出现瓶颈。

###### TUN模式：

IP隧道是一种数据包装技术，它可以将原始数据包封装并且添加新的报头，将其发送给真正的服务器。一般用于VPN等。数据流图如下：
![image-20231016190254377](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161902587.webp)

与NAT相比，RealServer直接返回客户机，修复了NAT中Director的瓶颈问题。

TUN模式的基本属性和要求：

1. 各个RIP不需要处在同一网段，但是RIP必须和公网通信
2. RealServer的TUN接口上需要配置VIP地址，以便接受Director的数据包以及作为响应报文的源IP。
3. 隧道位于Director和RealServer之间，隧道外层的IP头部的源IP是DIP，目标IP是RIP。而隧道内层的IP头可以分析得到源IP（VIP）和目标IP（CIP）。
4. 需要添加一条特殊路由，使得后端服务器返回客户端的时候源IP为VIP。

###### DR模式：

直接路由模式（Direct Routing）：DR模式中LVS依然只承担入站请求和选取服务器的任务，由后端真实服务器将其发回客户端。与隧道模式不同的是，RIP需要处在同一网段之中，返回的数据包中，源地址是VIP地址，目标地址是CIP地址。既不修改也不封装IP报文，而是将数据帧的MAC该为真正的MAC地址。

工作流图如下：
![image-20231016192757568](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161927813.webp)

要求和DR和TUN模式类似，但是RIP必须在同一网段。

三种模式的比较：

| 比较项   | NAT        | TUN          | DR         |
| -------- | ---------- | ------------ | ---------- |
| 真实网关 | 负载调度器 | 自有路由器   | 自有路由器 |
| IP地址   | 公网+私网  | 公网         | 私网       |
| 优点     | 安全性高   | Wan加密数据  | 性能最高   |
| 缺点     | 效率低     | 需要隧道支持 | 无法跨LAN  |

##### LVS调度算法

LVS有八种调度算法：

1. 轮询调度
2. 加权轮询调度
3. 最小连接调度
4. 加权最小连接调度
5. 基于局部性的最少链接
6. 带复制的基于局部性的最少链接
7. 目标地址散列调度
8. 源地址散列调度

调度算法与操作系统课程中的思想类似，此处不加赘述，感兴趣者可自行了解。

##### LVS典型方案

- LVS+Keepalived：

	keepalived是一个用于实现高可用性的软件，它通常与LVS结合使用。keepalived通过检测服务器的状态，例如网络连通性和服务可用性，来决定是否切换负载均衡器的主备角色。当主负载均衡器发生故障或不可用时，keepalived会自动将备负载均衡器切换为主角色，确保服务的持续可用性。

#### 负载均衡方案Nginx

##### 理解Nginx应用场景

Nginx是俄罗斯人编写的轻量级Web服务器。

什么是反向代理？客户端无感知代理的存在，以代理服务器接受Internet上面的请求，并将服务器上的结果返回请求的客户端。

什么是正向代理？当客户端无法访问外部资源的时候（比如墙），可以通过一个正向代理间接地去访问，所以正向代理是客户端需要配置代理服务器的ip。

###### Nginx基本模块

- upstream模块：定义后端服务器列表，例如：

```
# 动态服务器组
upstream dynamic_server{
	server localhost:8080;
	server localhost:8081;
}

# 其他页面反向代理
location ~.*$ {
	index index.jsp index.html;
	proxy_pass http://dynamic_server;
}
```

- `location ~.*$`：这是一个正则表达式模式，表示匹配所有请求URI。`~.*$`的意思是忽略大小写的匹配任意字符，并且`$`表示匹配结尾。

##### 理解Nginx调度算法

目前Nginx服务器的upstream模块支持六种方式的分配：分别是轮询，权重，依据ip的分配，最小连接时间，相应时间和依据url的分配。

##### Nginx的高可用负载均衡架构

![image-20231016195718716](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310161957039.webp)

上述方法是主从服务器的架构图，使用Nginx和Keepalived实现了高可用性和安全性。

## 云计算

### 云计算基础

#### 基础知识

![image-20231021163237669](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211632769.webp)

云计算是一种基于网络的计算模型，它通过互联网连接和共享的方式，提供各种计算资源和服务，包括计算能力、存储空间、数据库、应用程序等。

云计算的主要特点是将计算和存储资源从本地的物理设备转移到云服务提供商的服务器上。

云计算主要分为三种服务模型：IaaS（如OpenStack)，PaaS(如阿里云等)，SaaS(如讯飞听见)。

平台的发展趋势是ABCD融合：A人工智能(AI)，B大数据(Big Data)，C云计算(Cloud)，D物联网(Device)。

云计算的关键技术在于虚拟化，虚拟化的思想将资源抽象成为共享的资源池，然后由云平台从资源池分配资源。

![image-20231018115943283](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181159548.webp)

服务器虚拟化由多种技术架构，其中主要的四种如下：

![image-20231018173203055](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181751512.webp)

典型方案是KVM和Xen两种，KVM（Kernel-based Virtual Machine）和Xen是两种常见的开源虚拟化方案。

KVM是一种基于Linux内核的虚拟化技术。它利用Linux内核中的虚拟化扩展（如Intel的VT或AMD的AMD-V）来实现硬件虚拟化，允许多个虚拟机在同一物理服务器上同时运行。KVM提供了直接访问物理硬件的能力，因此在性能方面表现良好。KVM是Linux内核的一部分，因此无需额外的内核模块。

Xen是一种基于虚拟机监视器（Hypervisor）的虚拟化技术。它通过将操作系统直接运行在硬件上，实现了虚拟机的创建和管理。Xen支持多种操作系统，包括Linux、Windows和其他一些主流操作系统。

典型的云服务实例：云服务由很多种，包括云主机、私有云、弹性伸缩、负载均衡等等。常见的需求和方案对应的云服务如下：

![image-20231018175947141](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181759399.webp)

“标准云”的架构可以分为三层：云管理平台、云平台和资源层。三层对应的著名产品如下：
![image-20231021110156554](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211102854.webp)

#### 虚拟化技术

前面我们已经讲过，云计算最重要的一步就是将硬件资源虚拟化。虚拟化技术可以将计算机的硬件资源（如处理器、内存、存储和网络）进行抽象，使得多个虚拟机可以共享这些资源，并在逻辑上与物理计算机相隔离。这样，每个虚拟机就可以在自己的虚拟环境中运行操作系统和应用程序，就像独立的计算机一样。

虚拟化技术经过多年发展，已经成为一个庞大的家族，其中技术繁多，但是主要有四个分类指标：

##### 按照实现方法分类

按照实现方法分类的分类标准是通过软件辅助还是硬件辅助的方式实现对物理资源的访问拦截并且重定向。

按照上面的标准，可以将虚拟化分为软件辅助的虚拟化和硬件支持的虚拟化。

- 软件辅助的虚拟化

通过软件让客户机的特权指令陷入异常，从而触发宿主机进行虚拟化处理，实现对物理资源的截获和处理。常见的软件虚拟化工具有**QEMU和Vmware Workstation**。

优点：成本低廉，部署方便，管理维护简单

缺点：额外性能开销，增加了复杂性

- 硬件支持的虚拟化

通过物理平台本身提供的特殊指令，实现对硬件资源的模拟与支持。常见的有**Intel的VT-x和AMD的AMD-V**。

优点：性能优势，提供不同位数的操作系统支持

缺点：成本较高，维护麻烦

##### 按照实现机制分类

- 半虚拟化

通过对客户操作系统内核进行修改，加入特定指令，直接调用硬件资源，避免由VMM层转换指令带来的性能开销。典型的**半虚拟化技术如Xen**。

- 全虚拟化

客户机的操作系统是不需要做任何修改的，客户机操作系统和底层硬件完全隔离，由VMM转化成底层调用代码，具备良好的兼容性。**如KVM，VMWareStation。**

##### 虚拟化技术分类

- 裸机架构：直接使用VMM管理软件
- 宿主架构：硬件之上有一个普适操作系统
- 混合架构：兼而有之，**其中KVM属于混合虚拟化**。

还有其他的许多虚拟化技术，感兴趣者可自行查阅相关资料。

#### 云计算典型方案

下面我们将讲一下深信服的服务器虚拟化方案aSV、分布式存储方案aSAN、网络虚拟化方案aNET和深信服HCI超融合方案，这一部分直接了解即可，后面会单独详细讲解。

##### 服务器虚拟化方案——aSV

![image-20231018182330353](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181823623.webp)

##### 分布式存储方案——aSAN

![image-20231018182432624](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181824926.webp)

##### 网络虚拟化方案——aNET

![image-20231018182516725](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181825999.webp)

##### 深信服超融合方案HCI

![image-20231018182617359](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181826692.webp)

#### 容器技术

- **Kubernetes**是一个开源的容器编排和管理平台，用于自动化部署、扩展和管理容器化应用程序，不是虚拟化技术。

##### 容器概述

容器技术是一种虚拟化技术，用于将应用程序及其所有依赖项打包成独立的运行时环境，称为容器。每个容器都是相互隔离的，拥有自己的文件系统、进程空间和网络接口。容器可以理解成为“运行在一个操作系统上的一个独立系统”。

![image-20231021162923235](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211629334.webp)

- chroot技术

chroot，即change root directory，改变root目录。在Linux中，根目录为/，执行上述指令可以改变用户的根目录。

- namespace技术

Linux namespace是Linux提供的一种内核级别环境隔离方法，提供了对UTS、IPC、mount、PID和Network等的隔离机制。

- Cgroup技术

与namespace技术类似，也是将程序进行分组。但是Cgroup是对一组进程进行统一的资源监控和限制。

容器规范：OCI

![image-20231018183649557](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181836779.webp)

![image-20231018183710065](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181837337.webp)

##### 典型容器——Docker

Docker是使用最广泛的开源容器引擎，是一种操作系统级的虚拟化技术，依赖于namespace和Cgroups技术。

Docker的基本组成如下：

![image-20231018183931847](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181839117.webp)

具体的Docker容器可以自行查阅资料学习。

#### 云原生

云原生是一种构建和运行应用程序的方法，是技术体系和方法论。在云原生的架构下，开发模式，应用架构，部署运维都应当以云平台为基础：

![image-20231018184308771](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181843006.webp)

云原生由四个要素：

1. 微服务：几乎每个云原生的定义都包含微服务。
2. 容器化：容器化为微服务提供保障。
3. DevOps：DevOps是一个敏捷思维，是一个沟通文化，也是组织形式。
4. 持续交付：不误开发的交付，不停机实现更新，小步快跑，与传统的瀑布模型不同。

> DevOps是一种融合软件开发（Development）和信息技术运维（Operations）的文化、方法和实践，旨在实现快速而可靠的软件交付和持续改进。DevOps的主要目标是通过增强开发团队和运维团队之间的协作和沟通，实现更快速、更频繁的软件交付。它强调自动化、持续集成和持续交付等实践，以减少部署错误、加快软件交付时间和提高系统稳定性。

![image-20231021160331229](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211603341.webp)

### 云计算关键技术

云计算的关键技术是虚拟化技术，而虚拟化技术又主要包括计算虚拟化、存储虚拟化和网络虚拟化。先来看一下虚拟化的几个基本概念：

![image-20231018185123724](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181851942.webp)

IDV（Integrated Desktop Virtualization）是一种虚拟化技术，它允许将整个桌面环境虚拟化并交付给用户。**IDV技术将操作系统、应用程序和用户数据打包到一个虚拟桌面中，并通过网络将其传输到用户的终端设备上**。

![image-20231021162425358](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211625919.webp)

![image-20231021162748995](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211627113.webp)

#### 计算虚拟化



#### 内存虚拟化



#### 网络虚拟化

### OpenStack

#### CMP介绍

CMP(Cloud Management Platforms)是指云管理平台，是一种管理公有云、私有云和混合云环境的整合性产品。CMP可以用来管理OpenStack云环境，而Horizon只是用来管理OpenStack的Dashboard的。因此，CMP往往是以应用为中心的，而OpenStack是以基础设施为中心的。

#### OpenStack

OpenStack是一个开源的云计算平台，用于构建和管理私有云和公有云基础设施。它提供了一组模块化的软件组件，用于实现虚拟化、网络、存储和计算等基础设施服务。OpenStack的主要组件如下：

1. **Nova（计算）**：Nova是OpenStack的计算组件，负责管理和调度计算资源。它可以创建和管理虚拟机实例，并提供弹性计算能力。
2. **Neutron（网络）**：Neutron是OpenStack的网络组件，用于提供网络服务和管理网络资源。它支持虚拟网络的创建、子网和路由的配置，以及负载均衡和防火墙等网络功能。
3. **Cinder（存储）**：Cinder是OpenStack的存储组件，用于提供持久化块存储服务。它可以创建和管理块存储卷，并将其附加到虚拟机实例中。
4. **Swift（对象存储）**：Swift是OpenStack的对象存储组件，用于存储和检索大规模的非结构化数据。它提供了可扩展、冗余和持久的对象存储服务。
5. **Keystone（身份认证）**：Keystone是OpenStack的身份认证组件，用于管理用户、角色和权限等身份信息。它提供了统一的身份认证和授权机制，以确保只有经过授权的用户可以访问OpenStack的服务。
6. **Horizon（管理界面）**：Horizon是OpenStack的Web管理界面，用于管理和监控OpenStack的各个组件。它提供了直观的图形界面，使用户可以方便地管理云基础设施。
7. **Glance（镜像服务）**：Glance是OpenStack镜像服务的组件。

OpenStack的设计基本上是按照亚马逊设置的，可以将OpenStack理解为开源版本的AWS。OpenStack火起来的原因一个是Apache旗下的开源软件，另外就是使用python进行编写。

### 云计算交付和运维体系



### 云计算商业应用



## 存储基础

### 存储方式

![image-20231021114422063](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211144206.webp)

外挂存储的分类：
![image-20231021115335090](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211153332.webp)

#### DAS——直连式存储

DAS(Direct Attached Storage)即开放系统的直连式存储，即主机设备或者计算设备直接通过物理接口和线缆连接存储磁盘，从而获得存储资源。是一种连接方式，不是协议。可以是服务器上自带的硬盘或者线缆直连硬盘盒。DAS与服务器之间的通道通常采用SCSI连接。

![image-20231021115639268](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211156540.webp)

DAS有两种模式，JBOD的RAID。

![image-20231021120040252](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211200496.webp)

DAS架构特点：

1. 资源浪费，不利于共享：DAS直接挂在服务器上，随着需求增大，服务器和存储的设备数量变多，资源利用率地下。
2. 占用服务器资源大：以来主机操作系统进行IO读写和存储维护。
3. 扩展性差：采用SCSI连接，资源有限。
4. 管理性差：由原设备厂商提供升级和扩展。

#### SAN——存储区域网络

SAN是英文Storage Area Network的缩写，通常译为“存储区域网络”，它是一种在服务器和外部存储资源或独立的存储资源之间实现高速可靠的访问网络。其工作原理示意图如下：

![image-20231021142447574](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211424735.webp)

SAN存储，主要包括两种网络架构的SAN，一种是基于IP的SAN，一种是基于FC网络的SAN。两种架构的对比如下：
![image-20231021142641606](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211426782.webp)

![image-20231021142705777](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211427016.webp)

#### NAS——网络附加存储

NAS(Network Attached Storage)：网络附加存储，是一种集中存储、支持备份、可实现跨平台的数据共享，兼容性好的存储结构。特点如下：

1. 支持网络文件共享协议NFS，CIFS
2. NAS有自己的文件系统
3. 基于TCP/IP协议

* NAS和CIFS协议

NFS(Network File System)即网络文件系统，是FreeBSD支持的文件系统中的一种，适用于**类Unix操作系统**，允许网络中的计算机之间通过TCP/IP共享网络资源。在NFS中，本地NFS客户端应用可以透明地读写服务器上的文件，就像访问本地文件一样。

CIFS(Common Internet File System)仅适用于Windows操作系统，使客户端可以远程访问Internet计算机上的文件并提供服务，CIFS使用客户端服务器模式。

对比：CIFS面向网络连接的共享协议，常用TCP协议，而NFS可使用TCP或UDP。NFS缺点之一，要求client必须安装专用软件，而CIFS集成在OS内部，无需额外添加软件。

以下是DAS，NAS和SAN三种存储架构的对比：
![image-20231021144339995](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211443203.webp)

### 存储协议

不同的架构下，有不同的存储协议。下面是DAS，NAS和SAN的存储协议示意图：

![image-20231021145110855](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211451131.webp)

这些协议作了解即可。

### RAID技术

RAID(Redundant Array of Independent Disk)即独立磁盘冗余阵列，通常简称为磁盘阵列。有软件RAID和硬件RAID两种连接方式。

RAID技术有三个关键概念：镜像(Mirroring)、数据条带(Data Stripping)和数据检验(Data parity)。

![image-20231021145752935](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211457197.webp)

业界和学术界一般把RAID分成七个标准的RAID等级，等级之间不存在高低之分。

![image-20231021145952455](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310211459697.webp)

