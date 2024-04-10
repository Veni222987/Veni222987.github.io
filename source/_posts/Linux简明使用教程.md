---
title: Linux简明使用教程
cover: https://www.chenwenguan.com/wp-content/uploads/2021/03/linux-logo-e1615825628735.jpg
author: Veni
categories: notes
tags:
  - Linux
date: 2023-10-17 13:40:19
---

下面是Linux操作系统的简单，覆盖了Linux基本使用的常用指令。<!--more-->

# Linux简明使用教程

## 基本命令

### 命令行和快捷键

- 补全命令：使用Tab键可以补全命令
- `Ctrl+A`：光标移动到最前，`Ctrl+E`：光标移动到最后，`Ctrl+L`：清除当前窗口
- `ls -a -l -t`和`ls -alt`是一样的

### Linux文件

- 查看文件

![image-20231017151632556](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171516725.webp)

- 创建文件：使用mknod或者touch创建文件，使用mkdir创建目录
- 删除文件：使用`rm <文件名>`指令，使用`rm -r <文件名>`可以删除目录
- 移动与复制：使用`mv <源文件> <目标目录>`移动文件，使用`cp <源文件> <目标目录>`实现复制文件并移动。

- 创建链接文件：`ln -s <A> <B>`
- 常见的文件类型标识：

![image-20231017152604976](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171526100.webp)

- `chmod`是一个用于修改文件或目录权限的命令。它可以更改文件的读取、写入和执行权限，以及文件所有者和所属组的权限。

例如，将sh设置为可执行的指令为：

```shell
chmod -x script.sh
```



## Vim编辑器的使用

Vim有输入模式，命令模式和底行模式三种，其中命令模式和底行模式的区别在于`:`。

Vim的基础用法不加赘述，下面是Vim的底行模式的一些命令：

![image-20231017152932416](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171529532.webp)

![image-20231017152951580](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171529690.webp)

## 磁盘管理

### 查看磁盘空间：`df`指令

![image-20231017153314957](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171533093.webp)

使用`df -i`查看磁盘INodes使用情况，`df -h`使用合适的单位显示文件系统信息。

### 磁盘分区

使用`fdisk`指令可以对磁盘进行分区管理，但是只能划分小于2T的分区。由于磁盘分区使用频率较低，故不详细展开，使用时查阅fdisk指令即可。

### 磁盘挂载

在完成磁盘的分区和格式化之后，还需要挂载到系统路径，才可以使用。

使用mount命令可以手动挂载，示例如下：

![image-20231017154713601](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171547780.webp)

然而使用mount指令手动挂载的分区，在系统重启之后会取消挂载，要实现开机自动挂载的方法是使用配置文件/etc/fstab。

## 用户管理

Linux有三类不同的用户：root用户，一般用户和服务用户。用户的管理文件在/etc/passwd文件中。一般而言，一个用户的home目录在`/home/<用户名>`。

- 新增用户：使用`useradd`指令。

![image-20231017155501128](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171555253.webp)

- 删除用户：userdel指令，-r可以一并删除用户的home目录。
- Linux用户创建完成以后，默认是没有密码的，设置密码的指令是`passwd username`

## Linux压缩与解压缩

### Linux常见的压缩包格式

打包在linux下一般是tar，而压缩则可以是zip，gz等等。一般而言，在网上下载某些SDK的时候，最常见的压缩包格式就是tar.gz包。

![image-20231017160148882](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171601997.webp)

> 打包和压缩的区别：
>
> 打包是将多个文件和目录合并成为一个；
>
> 压缩是将单个文件使用压缩算法减小空间；

### 常见的压缩工具

#### zip压缩工具

zip可以压缩文件和目录，并且zip不会删除压缩的文件。

- 压缩方法：`zip <生成文件> <原始文件>`，使用-r可以递归压缩，也就是压缩目录。
- 解压方法：`unzip <压缩文件> -d <解压路径>`，如果不使用-d参数，则会解压到当前路径。

#### gzip压缩工具

gzip是用来压缩和解压缩.gz格式的文件的。

- 压缩方法：`gzip <文件名>`，使用-#可以指定压缩等级1~9，默认为6。
- 解压方法：`gzip -d <文件名>`

注意：gzip压缩和解压缩都会删除源文件。

#### bzip2压缩工具

bzip2是用来压缩和解压缩.bz2格式的文件的。

- 压缩方法：`bzip2 -z <文件名>` ，不用-z指令也可以压缩
- 解压方法：`bzip2 -d <文件名>`

> 注意：gzip和bzip2都不能压缩目录，因此需要使用tar将其进行打包。而使用tar打包成为一个文件后，则zip也就失去了打包目录的优势。所以tar一般配合gzip和bzip2使用。

### 打包和解包工具——tar

熟记下面的参数，即可流畅使用tar。

![image-20231017162706505](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171627644.webp)

记忆方法：

`-z -j -J`三选一，标识压缩方式，分别是gz, bz2和xz；`-x`表示解包，`-c`表示打包，不能同时出现。`-v -f`一般都有，方便指定文件和观察打包/解包过程。如果是打包，则指令格式为：`tar -cvf <目标文件名> <源文件名>`，而解包后面可以直接接文件名，默认是解包到当前文件夹，如果需要打包到指定文件夹，则指令后面需要加上`-C <目标路径>`。

## Linux软件安装

### 安装包管理

在早期的Linux系统中，如果想要添加软件，必须获取源码，然后编译成二进制代码再运行，这种软件包通常是一个压缩包，比如tar.gz模式。直到现在，某些个人开发者开发的软件也还在使用这种打包方式。

后来，为了解决从压缩包安装软件的麻烦，软件包发展出了RPM包和Deb包两种形式。

- RPM包：最初是Red Hat Package Manager，后被重命名为RPM，是SUSE，Red Hat和Centos等发行版的首选软件包格式。
- Deb包：基于Debian GNU/linux的管理包文件，常用于Debian发行版本比如Ubuntu，Linux mint等等。

### 包管理工具

#### apt(Advanced Package Tool)：

1. apt是Debian和Ubuntu等基于Debian的Linux发行版中的包管理工具。
2. apt通过命令行工具apt-get和apt-cache提供了用户友好的接口，用于搜索、安装、升级和删除软件包。
3. apt使用.deb软件包格式。
4. apt还提供了aptitude和Synaptic等图形化前端工具，简化了软件包管理过程，具备自动下载和安装依赖的能力。

#### yum(Yellowdog Updater Modified)

1. yum是Red Hat、CentOS和Fedora等基于Red Hat的Linux发行版中的软件包管理工具。
2. yum通过命令行工具yum提供了用户友好的接口。
3. yum使用.rpm软件包格式。
4. yum还具有自动解决依赖关系的能力，可以自动下载和安装软件包的依赖项。

### 源码安装软件

有一些开源软件，需要自行使用gcc等编译，这种场景无法统一，可以查阅官方文档结合日志搜索解决。

## Linux网络配置

将单一的桌面PC连接到网络是一件容易的事情，但是将Linux服务器连接到网络，比如静态IP，VPN和代理服务器等等。学习网络配置管理，不仅要学会使用网络，更重要的是掌握网络状态，排查系统问题。

传统的Linux网络配置通过network.service来实现，常见的命令有`ifconfig, ifup, ifdown`，用于查看，启动和关闭某个网络接口，比如eth0等等。

后来，Linux系统诞生了NetworkManager这样的管理工具，但是两种工具使用一种即可，否则会有冲突。

### NetwotkManager管理网络

#### 通过nmtui管理网络

想通过nmtui管理网络的前提是网卡可以直接被NetworkManager管理，在Debian发行版下，下载安装NetworkManager的指令如下：

```shell
sudo apt-get install network-manager
```

安装完成后，可以使用`nmcli device`查看网络设备:

![image-20231017181234679](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171812813.webp)

直接输入nmtui即可使用GUI管理网络，也可以通过修改配置文件直接管理网络。

> nmtui是NetworkManager Text User Interface的缩写，是一个基于文本的图形界面工具，用于配置和管理网络连接。而nmcli则是通过命令行的形式管理计算机网络。

在修改网络之后，需要重启网络服务以生效：

```
systemctl restart network
```

#### 其他网络指令

- netstat：使用netstat可以查看整个Linux系统的网络情况

![image-20231017182432059](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171824263.webp)

最常用的指令`netstat -antp`，运行结果如下：

![image-20231017182650172](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171826399.webp)

- ss命令：SocketStatistics的缩写，用来获取socket的统计信息

当服务器的socket连接数量变得非常大的时候，无论是使用netstat命令还是直接cat /proc/net/tcp，执行速度都会很慢，ss用到了TCP协议栈中的tcp_diag，可以获得linux内核的第一手信息，效率极高。

![image-20231017183018127](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171830301.webp)

最常用指令：`ss -lntp`，以数字形式显示tcp连接，并显示进程名：

![image-20231017183137109](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171831280.webp)

## 进程管理

Linux的进程在/proc目录下，相关信息存在于以ID命名的目录中。

> Linux一切皆文件！

- 查看进程：ps指令

`ps ux|less`：查看系统当前用户的所有进程，| less是管道处理，分页显示结果

`ps -A`查看所有进程

- 关闭和修改进程：kill指令

使用kill指令可以对某个进程发出信号，常见的信号如下：

![image-20231017183953235](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171839352.webp)

例如，在确保安全的情况下，可以使用`kill -9 1234`强制关闭1234进程。

使用killall指令可以直接根据进程名杀死进程，例如`killall bash`。

- 修改CPU使用优先级：nice指令

Linux使用nice值定义进程访问CPU的优先级，位于[-20,19]区间，默认取值为0。

1. nice值越低，访问CPU优先级越高，默认0。
2. 普通用户只能将自己的进程nice设为0~19。
3. 普通用户只能设置nice值变高。
4. 普通用户只能在自己的进程上设置nice。

```
nice -5 vim &
```

将vim进程放在后台运行，并且设置nice值为0+5

```
renice -n -5 1234
```

对进程ID为1234的nice减少5。

- 守护进程：所有进程之母

一般守护进程是init或systemd，位于进程表的第一行，Linux内核中有一个PID为0的进程，表示内核，守护进程的PID则设置为1。

> 守护进程（daemon）是在操作系统后台运行的一种特殊类型的进程。它们通常在系统引导时启动，并且在整个系统运行期间持续运行。守护进程通常不与用户交互，而是在后台执行特定的任务或提供某种服务。它们被设计为在系统启动时自动运行，并且通常会一直运行，直到系统关闭或手动停止。

- 服务管理：

Linux下服务管理有两个指令，分别是service和systemctl指令，systemctl是Linux的最新初始化系统，兼容了service的指令。

> service和systemctl指令的本质都是执行/etc/init.d目录下的脚本

systemctl常用方法如下：

![image-20231017185227218](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171852378.webp)

## Linux运维管理

### 性能与服务

- 系统状态监控：`vmstat`，直接使用则输出当前的系统状态，第一个数字是持续输出的间隔秒数，第二个数字是持续时长秒数。例如`vmstat 1 5`表示间隔1s持续执行5次。

![image-20231017185513224](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171855446.webp)

- 内存资源监控：`free`

使用free指令可以查看系统的内存使用状态，如下图

![image-20231017185749475](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171857589.webp)

### 计划任务

使用`crontab`可以编辑计划任务，选项如下：

1. -u 指定用户名，不指定为当前用户
2. -e 编辑计划任务
3. -l 列出计划任务
4. -r 删除所有计划任务

创建单个计划任务的方法如下：

![image-20231017190131628](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171901769.webp)

如需要周期性地执行某些计划任务，则可以写成如下形式：

![image-20231017190308070](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171903248.webp)

### 系统服务管理

系统服务是在操作系统中以守护进程形式运行的软件组件，用于提供特定的功能或服务。这些服务在系统启动时自动启动，并在后台持续运行，以响应系统和用户的需求。

Linux的系统服务一般位于`/usr/lib/systemd/system`路径下，具有每个系统服务的内容：

![image-20231017190730361](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171907744.webp)

> 后缀名解释：
>
> service:系统服务，如crond，sshd都是服务，包括自己安装的mysql-server等。
>
> target:多个unit组成（unit是一个逻辑概念，该目录下的都可以叫做unit，比如service等，target就是组合的unit）
>
> device:硬件设备
>
> mount:文件系统挂载点
>
> automount:自动挂载点

- 创建系统服务：

在服务路径下创建.service文件，该文件由三部分组成：

1. [Unit]描述、文档等等
2. [Service]类型，环境变量，具体指令
3. [Install]服务安装到的target

例如，某个service文件如下：

![image-20231017191507265](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171915497.webp)

使用不同的target可以设置不同的运行级别，使用`systemctl get-default`可以查看当前的运行级别。

- 自启动服务

使用`systemctl enable <服务>`可以设置服务自启动，反之，使用`systemctl disable <服务>`可以禁止服务自启动。

![image-20231017192014594](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171920730.webp)

### 防火墙

Linux下管理防火墙的软件比较多，在CentOS的5，6版本和Debian中主要使用iptables，在CentOS 7默认的防火墙管理工具是firewalld。主要学习iptables的配置方法，学有余力可以学习其改进版Shorewall。

iptables是Linux系统中最常用的防火墙管理工具之一。它是一个基于内核的防火墙管理工具，通过操作netfilter框架实现网络包过滤和网络地址转换。Shorewall是一个基于iptables的高级防火墙管理工具，适用于各种Linux发行版。它提供了一个配置文件来定义防火墙规则，可以简化复杂的防火墙配置。Shorewall支持多种方式的配置，包括命令行和文本配置文件。

#### iptables

iptables由四个表，分别是：

1. filter:默认表，用常用于过滤数据包
2. nat:用于地址转换，侧重于连接管理
3. mangle:侧重于每一个数据包管理，比如修改TTL
4. raw:异常处理

iptables五个链：

1. input:处理进入系统的网络包
2. output:处理从系统发送出去的网络包
3. forward:处理通过系统转发的网络包
4. prerouting:在路由决策之前处理进入系统的网络包
5. postrouting:在路由决策之后处理即将离开系统的网络包

iptables四表五链的对应关系：

![image-20231017193410854](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171934972.webp)

四表五链关系理解：

1. filter表是最常用的表格，用于过滤网络包。它包含了三个默认的链（chains）：INPUT、OUTPUT和FORWARD。这些链用于处理进入系统、离开系统和通过系统转发的网络包。通过在这些链上配置规则，可以控制网络包的接受、拒绝、转发等。
2. nat表用于网络地址转换（Network Address Translation，NAT）。它包含了三个默认的链：PREROUTING、OUTPUT和POSTROUTING。这些链用于在网络包进入和离开系统之前或之后修改包的源地址、目标地址、端口等。
3. mangle表用于修改网络包的特定字段。它包含了五个默认的链：PREROUTING、INPUT、FORWARD、OUTPUT和POSTROUTING。这些链用于在不同的阶段对网络包进行修改，例如修改TTL字段、标记包、更改包的QoS等。
4. raw表用于进行特定的包处理，如连接追踪（connection tracking）和数据包跳过（packet bypass）。它包含了两个默认的链：PREROUTING和OUTPUT。这些链允许在数据包进入和离开系统之前进行特定的处理和跳过。

数据包的处理顺序如下：

![image-20231017194101680](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171941869.webp)

流量包通过防火墙，实际就是在这个处理顺序中检验是否满足规则，定义iptables规则的方法如下：

![image-20231017194230628](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171942799.webp)

- iptables查看规则：`iptables -nvL --line-number`

![image-20231017194700960](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171947100.webp)

该指令可以查看不同的链上的所有规则信息。

- iptables规则编写：

新建一条规则，禁止目标端口为TCP22的访问，放在INPUT链的最后：

```shell
iptables -A INPUT -p tcp --dport 22 -j DROP
```

指定某个规则序号删除指定规则，可以先使用上面的查看规则的指令获取序号，然后删除规则：

```shell
iptables -D INPUT 1
```

其他示例：

![image-20231017195128127](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171951292.webp)

![image-20231017195206347](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310171952499.webp)

## FTP服务器配置

FTP(文本传输协议)，是一个用于在计算机网络上进行文件传输的应用层协议，一般运行在TCP20和TCP21两个端口，20端口用于在客户端和服务器之间传输数据流，21端口用于传输控制流，并且通向FTP服务器入口。

FTP有两种工作模式，主动模式下，客户端开启端口，服务器主动连接客户端的端口；被动模式下，服务器开启端口，被动等待客户端连接。

### FTP配置：vsftpd

在Linux中，一般使用vsftpd进行FTP服务配置，vsftpd是很多发行版本的默认软件，但是如果系统没有安装，则需要自行安装。在Debian系统上，配置vsftpd的文件位于/etc/vsftpd.conf。打开配置文件，可以发现：主动模式默认使用20端口进行连接。

![image-20231017200833224](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172008330.webp)

### vsftpd账号配置

vsftpd使用三种账号访问，匿名帐号，linux本地帐号和虚拟账号，推荐使用虚拟账号访问。使用虚拟账号访问的时候，建议关闭匿名帐号的相关权限，修改方法同样是编辑vsftpd.conf文件：

![image-20231017201301462](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172013571.webp)

开启了虚拟账号登录之后，我们需要对虚拟账号进行管理，首先需要配置vsftpd.conf：

![image-20231017202150233](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172021363.webp)

接下来就是准备上面配置中的东西，首先是创建一个虚拟账号对应的系统用户名，使用-s禁止该用户登录：

```shell
useradd virtual_ftpuser -s /sbin/nologin
```

接下来在/etc/vsftpd/vsftpd_login文件下加入用户信息，创建test1和test2两个账号，并且指定其密码。
![image-20231017202521753](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172025851.webp)

然后生成账号的数据库文件，在Debian发行系统中需要自行安装db_load或使用其他命令（如db_load5.3）替代。

```shell
db_load -T -t hash -f /etc/vsftpd/vsftpd_login /etc/vsftpd/vsftpd_login.db
```

接下来配置两个用户的权限：在下面的目录创建两个文件：

/etc/vsftpd/virtual_ftpuser_conf/test1

/etc/vsftpd/virtual_ftpuser_conf/test2

然后输入以下内容：

![](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs%2Fvblog%2Fimage-20231017220552283.webp?q-sign-algorithm=sha1&q-ak=AKIDCbLd2jCUzkODBH4_S889m9PJWVKQ4ALCobKv1siJvZ8t0ACnZnNEXCIWIKoQZk0d&q-sign-time=1697551818;1697555418&q-key-time=1697551818;1697555418&q-header-list=&q-url-param-list=ci-process&q-signature=dcebd997749108a08bdcdb3e1c2ddc5df5ae450f&x-cos-security-token=r1H4WgNZExkVuYObwUtENLIhx2aBgJza091351a026a984b1330be7a6566bc7a8Z9fn7Qa58nqDuWUebadOp82oSHapqWPvXiPJAZFUxB9Np--oPJHQmK9oLQ1a_cbfiZ1VLpRUaN8PZ3BhrYxshlh4zeZ-epvl1SN2NZNVVFrXh3X3fB-Tp_EFrl8hU-luVRsdVpXCYfPfxHDdoWjeq9rE7EGUYWRghiHuRMAcu3M7fAha0pvdMWdobCR0XqSv&ci-process=originImage)

接下来创建文件和目录，分别所谓两个虚拟用户的访问根目录，并且在里面生成两个文件，当这两个用户登陆的时候，最开始看到的应该就是自己对应的文件。
![image-20231017221212222](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172212442.webp)

完成了创建账户之后，我们就可以调用这些账号进行认证了，认证方法如下：

![image-20231017221335233](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310172213436.webp)

接下来重启系统服务即可：

```shell
systemctl restart vsftpd
```

后面就可以使用ftp访问当前服务器了，并且可以实现虚拟用户登录。

## Shell脚本

### Shell脚本简介

shell脚本在Linux的shell中工作，并不是一门编程语言，而是命令的集合，类似windows的bat文件。

> shell和bash之间的关系：
>
> Shell是一种命令行解释器（command-line interpreter），它是操作系统与用户之间的接口，用于解释和执行用户输入的命令。而Bash（Bourne Again Shell）是一种Unix shell，它是Shell的一种实现，也是目前最常用的Shell之一。

在编写好shell脚本之后，需要赋予执行权限，才可以直接运行：

```shell
chmod -x test.sh

./test.sh
```

下面编写一个shell脚本示例：

```shell
#!/bin/bash
d=`date +%H:%M:%S`
echo "script start at $d"
echo 'wait 2 secs'
sleep 2
d1=`date +%H:%M:%S`
echo "the script end at $d1"
```

\`date +%H:%M:%S\`是一个整体的命令，需要用反引号括起来，表示获取当前时间的HHMMSS格式，echo是将内容送到控制台，当echo显示的内容有变量的时候，需要使用双引号，否则单引号即可。$表示获取变量。

执行结果如下：

![image-20231018095811226](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310180958386.webp)

### Shell脚本语法

#### 数学运算

数学运算需要用中括号括起来，要赋值的话还要加$号，例如：

```shell
a=1
b=2
sum=$[$a+$b]
echo "$a+$b=$sum"
```

#### 用户输入

获取用户交互输入：

```shell
read -p "please input a number x :" x
read -p "please input a number y :" y
sum=$[$x+$y]
echo "x+y=$sum"
```

其中-p是设置一条提示消息，用于提示用户需要输入的内容，其后直接跟变量名，无需声明。

#### 脚本参数和脚本选项

编写一个脚本，获取其脚本参数的值并相加

```shell
sum=$[$1+$2]
```

其中\$1和\$2代表第1个和第2个参数，而\$0则表示该脚本名字本身。

脚本则是带`-`的参数，比如`ls -l`，在实际执行脚本的时候，可以省略脚本选项。一般来说需要使用额外的脚本选项解析工具如getopt来解析，例如：

```shell
while getopts ":a:b:c" opt; do
  case $opt in
    a)
      echo "Option -a selected with value $OPTARG"
      ;;
    b)
      echo "Option -b selected with value $OPTARG"
      ;;
    c)
      echo "Option -c selected"
      ;;
    \?)
      echo "Invalid option: -$OPTARG"
      ;;
  esac
done
```

在上面的示例中，`getopts`命令用于循环遍历脚本的选项。每个选项都通过`case`语句进行处理，其中`$opt`表示当前选项，`$OPTARG`表示选项的参数值。

#### 条件判断

- if语句逻辑判断：

```shell
read -p "input your score : " a
if [ $a -gt 80 ];then
	echo "good job"
elif [ $a -gt 60 ];then
	echo "you pass the exam"
else
	echo "you failed"
fi
```

逻辑表达式使用[]括起来，并且中括号内部两侧都需要有一个空格。-gt是greater than的缩写，其他的证书比较缩写如下图：

![image-20231018102922511](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181029738.webp)

- case条件判断

case的语法如下：

```shell
case $Num in
	1) echo 'Select 1'
	;;
	2) echo 'Select 2'
	;;
	4|5) echo 'Select 4 or 5'
	;;
	*) echo 'Select othor'
	;;
esac
```

shell脚本有严格的语法限制，注意分号的位置为下一行的开始，使用*表示其他的任意匹配。

#### 循环

- for循环

for循环是一个十分常用的循环结构，常见的用法如下：

```shell
for <var> in <cases> ;do
	command
done

```

- while循环

while循环在实际使用中可能会写成一个死循环，用于监控脚本,while语句的格式如下：

```shell
while <条件>; do
	command
done
```

- until循环，语法和while循环一样，但是满足条件的时候终止循环。
- break用于中断，continue用于循环。

下面有一些简单的练习，可供读者熟悉shell脚本编程:

![image-20231018110505536](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202310181105774.webp)

## grep，sed和awk

`grep`、`sed`和`awk`是在Unix/Linux环境中常用的文本处理工具，它们具有不同的特点和用途。

### grep

grep命令用于查找文件里面符合条件的字符串，或用于查找内容里包含指定范式的文件。

- 查找文件内容

语法为

```shell
grep <选项> <目标文字> <目标文件>
```

例如：

```shell
grep tcpdump /etc/passwd
```

查找passwd中tcpdump用户的相关信息。

- 查找指令内容，例如`ls -l | grep m*`

此外，所有的查找内容都可以使用正则表达式匹配。正则表达式的联系可以到[网站](https://tool.oschina.net/regex/)练习。

### sed

`sed`是一个流式文本编辑器，可用于对文本进行替换、删除、插入、查找等操作。它使用简单的命令来操作文本，并且可以通过正则表达式进行模式匹配和替换。`sed`通常用于在脚本中批量处理文本数据，或者通过管道操作处理文本流。

例如，一个使用sed将文本中的foo替换成为bar的示例：

```shell
echo "foo foo foo" | sed 's/foo/bar/'
```

输出结果为：`s/foo/bar/`。

### awk

`awk`是一种用于处理和分析文本数据的强大工具。它是一种完整的编程语言，具有变量、循环、条件语句等常见编程特性。`awk`通过对输入文本逐行处理，并根据指定的模式和动作来提取和操作数据。

使用awk命令从文本数据中提取第一列的示例：

```shell
echo "1,John,Doe" | awk -F',' '{ print $1 }'
```

输出结果为1。




#  Vscode免密登录

1. 使用ssh-keygen创建密钥对
2. 将公钥追加到authorized_keys，然后修改权限，重启sshd
3. 将密钥下载到本地主机
4. 配置.ssh/_config

![image-20240102183647945](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202401021836117.webp)
