---
title: 【Exp】Redis的使用与原理
date: 2023-08-02 14:54:53
cover: https://ts1.cn.mm.bing.net/th/id/R-C.556f913dcaca489b2c5b4ca636627e46?rik=7APNeYsdGR6aWg&riu=http%3a%2f%2fskrnet.cn%2fimages%2fredis_logo.png&ehk=8uu73ahGxQbMoHIADWKzd79RMMvfhM5%2bHj0zPwrHZHE%3d&risl=&pid=ImgRaw&r=0
categories: notes
tags:
- redis
- 笔记
---

Redis（Remote Dictionary Server）是一个开源的内存数据存储系统，也可以被看作是一个键值（key-value）存储数据库。本文将分成三个部分：Redis的入门教程、Redis底层数据结构和Redis架构模型。这是一个循序渐进的过程，读者可以按需阅读。

# Redis入门教程

redis可以一个特殊的数据库，它运行在内存之中，只有两个字段：键和值。其中的值可以是多种数据类型。

redis和数据库一样，都需要配置访问权限，默认的访问权限为本地主机，即127.0.0.1，默认端口是6379。

## 配置，启动，操作，关闭redis

### redis配置

- 控制访问权限：在配置文件中修改bind属性，一般来说配置文件位于/etc/redis.conf

- redis也可以设置账号密码，在Redis配置文件中，找到并编辑`requirepass`属性。如果该属性被注释掉了（以`#`开头），需要取消注释，并在等号后面设置一个密码。例如，可以设置`requirepass mypassword`，其中`mypassword`是你想要设置的密码。

### redis启动

三种方法：默认配置，运行配置，配置文件启动（**推荐使用配置文件使用**）

```
redis-server /etc/redis.conf
```

### redis操作

可以使用redis-cli连接服务器，有如下两种方法：交互式和命令式。

- 交互式：

```bash
redis-cli -h 127.0.0.1 -p 6379
>set hello world
OK
>get hello
"world"
```

- 命令式：

```
redis-cli -h 127.0.0.1 -p 6379 get hello
"world"
```

如果缺省了-h和-p两个参数，那么默认是链接127.0.0.1:6379。

### redis选择数据库

使用`SELECT <index>`可以选择某个特定的数据库。redis共有16个数据库可以使用，默认使用DB 0工作。

```bash
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
```

### redis关闭

```
redis-cli shutdown
```

redis关闭的过程：断开客户端连接，持久化文件生成。这要求我们自己生成持久化文件，redis并不会自动做这件事。这种情况是特殊情况主动关闭redis的操作，就好像关机之前需要保存工作区一样。

## redis基本命令

### 查看所有键

```
127.0.0.1:6379> keys *
1) "backup2"
2) "backup3"
3) "backup4"
4) "backup1"
```

### 键总数

```
127.0.0.1:6379> dbsize
(integer) 4
```

### 检查键是否存在

`exists [keys]`，如果键存在则返回1，否则返回0。

```
127.0.0.1:6379> exists backup1
(integer) 1
127.0.0.1:6379> exists backup1 backup5
(integer) 1
127.0.0.1:6379> exists backup1 backup2
(integer) 2
```

可以检查多个键，但是返回值是一个整数，表示查询的键中存在的个数。

### 删除键

```bash
del [keys]
```

返回值是成功删除的键的个数，如果删除失败，返回0，否则返回1。使用的语法和返回结果与上述的exists类似，不加赘述。

### 键过期

redis支持使用`expire key seconds`对键添加过期时间，当超过过期时间后，会自动删除该键：

```
127.0.0.1:6379> set hello world
OK
127.0.0.1:6379> expire hello 20
(integer) 1
127.0.0.1:6379> ttl hello
(integer) 14
```

使用ttl指令可以查看键的剩余时间。

### 键的数据类型和具体操作

前面说过，一个key对应的数据类型只有五种：`string,hash,list,set,zset`（有序集合）。

> 注意，我们这里将Redis对外展示的五种键类型称作“data types”，也就是数据类型。下文会将其底层实现称为“数据结构”或者“内部编码”。

下面是这五种数据类型的插入示例:

- 字符串string：

```bash
SET username "Veni"

127.0.0.1:6379> GET username
"Veni"
```

- 哈希hash：

```bash
HMSET user222 username "Veni" age 20

127.0.0.1:6379> HGETALL user222
1) "username"
2) "Veni"
3) "age"
4) "20"

127.0.0.1:6379> HGET user222 username
"Veni"
```

查询哈希需要使用HGET指令，指令格式为`HGET key field`获取特定key的某个字段的值。使用`HGETALL key`可以获取某个key的所有字段。

- 列表list：分为左插入和右插入，rpush是在数组的右侧追加，lpush为左侧追加，比如abc使用rpush就是[a b c]，而使用lpush结果是[c b a]。

```bash
rpush rlist a b c
lpush llist a b c

127.0.0.1:6379> LRANGE rlist 0 -1
1) "a"
2) "b"
3) "c"

127.0.0.1:6379> LRANGE llist 0 -1
1) "c"
2) "b"
3) "a"
```

使用`LRANGE`可以查询list的元素，0到-1表示查询所有值。修改区间起点和区间终点即可查询到不同的值。

- 集合set

```bash
SADD userset "veni" "moss"

127.0.0.1:6379> SMEMBERS userset
1) "moss"
2) "veni"

127.0.0.1:6379> SISMEMBER userset "veni"
(integer) 1
```

使用`SMEMBERS`可以查看集合中的所有成员，使用`SISMEMBER`可以查看某一个元素是否在集合里面。

- 有序集合zset：

```bash
ZADD userScore 650 "veni" 625 "moss" 610 "neil"

127.0.0.1:6379> ZRANGE userScore 0 -1
1) "neil"
2) "moss"
3) "veni"

127.0.0.1:6379> ZSCORE userScore moss
"625"

127.0.0.1:6379> ZREVRANGE userScore 0 -1
1) "veni"
2) "moss"
3) "neil"
```

使用`ZRANGE`查看有序集合里面的特定一批元素的名称，使用`ZSCORE`可以某个指定的元素的分数，`ZREVRANGE`按照分数从大到小列出元素名字。

- 查询key的类型：使用type指令可以查看某一个key的类型。

```bash
127.0.0.1:6379> type userScore
zset
```

# Redis底层数据结构

## 类型和编码的区别

上面我们已经说过了，type可以查看某一个键的类型，这里说的类型，是Redis五种对外使用的数据结构，实际上，每种数据结构底层都包含不止一种数据结构或编码方式。

举个例子，我们都知道set指令是对应String类型的，那么set一个整数、小数、字符串分别会对应什么数据类型呢？实践一下：

```shell
SET hello world
SET age 20
SET height 1.65
```

然后查询一下数据类型，使用TYPE指令：

![image-20240318142654832](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/image-20240318142654832.png)

但是使用`OBJECT ENCODING`指令查看内部编码方法的时候，发现了：hello键和height对应的embstr，而age对应的是int。

![image-20240318142805519](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/image-20240318142805519.png)

通过这个例子，相信大家也可以直观感受到数据类型和数据编码的区别。下面讲讲每种数据类型对应的编码有哪些。

## 数据类型的编码方式

在redis中，每种数据类型都有多种编码方式。Redis 会根据数据大小、类型以及具体的操作情况来自动选择和转换编码方式，以达到最佳的内存使用和性能效果。一旦有了更高效的编码方式，就可以直接修改内部编码，无需修改外部代码。

对于Redis中的存储结构，简化后的基本模型如下，ptr是一个void*指针，会根据encoding来指向不同类型的对象。

![](https://cdn.xiaolincoding.com/gh/xiaolincoder/redis/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B/int.png)

### String

字符串类型的内部编码有三种：

1. `embstr`：短字符串编码，用于存储较短的字符串数据。(小于等于64字节)
2. `raw`：简单字符串的默认编码，适用于存储一般文本数据。(大于64字节)
3. `int`：整数编码，用于存储整数类型数据。

Redis对于String类型数据的处理策略一般是：首先尝试能否转成int类型编码。如果转化失败，例如超出范围、出现了小数或字母等，就会根据串长度决定采用embstr或者raw类型存储。

### Hash

哈希类型的内部编码有两种：

1. `ziplist`：紧凑哈希编码，适用于较小的哈希
2. `hashtable`：哈希表编码，用于较大的哈希。

当哈希类型元素个数小于hash-max-ziplist-entries(默认为512个)，并且每个值的大小都小于hash-max-ziplist-value(默认64字节)的时候，Redis底层就会采用ziplist实现。当有任何一个条件不满足的时候，就会采用hashtable实现。

ziplist（压缩链表）是一种紧凑的结构，用连续的内存块存储数据结构，当存储的数据量变大的时候，ziplist的性能会下降，这个时候就会转而使用hashtable实现，hashtable的查询效率是O(1)的。

### List

1. `ziplist`：紧凑列表编码，适用于较小的列表。
2. `linkedlist`：双向链表编码，适用于较大的列表。

List和Hash类似，在元素个数和元素大小都小于默认值的时候，就会采用ziplist编码，否则采用linkedlist编码。值得注意的是，这个默认值和Hash是一样的，都是512和64.

两者的对比也很明显，ziplist是内存连续的，而linkedlist是非连续的。当存储的数据量变大或修改的时候，ziplist内存利用率会降低。

在Redis3.2之后，推出了quicklist，替代了上面两种结构，所以现在对list类型的键使用OBJECT ENCODING，返回结果都是quicklist。

### Set

集合类型的编码形式有两种：

1. `intset`：整数集合编码，适用于存储整数类型数据。
2. `hashtable`：哈希表编码，用于较大的集合。

当集合中的元素都是整数并且元素个数小于512的时候，会采用intset类型来编码，否则采用hashtable编码。

### ZSet

1. `ziplist`：紧凑有序集合编码，适用于较小的有序集合。
2. `skiplist`：跳跃表编码，用于较大的有序集合。

当且仅当集合元素小于128个并且每个元素的值都小于64字节的时候，会采用ziplist编码，否则采用skiplist编码。

> 什么是跳跃表编码？
>
> 跳跃表编码是一种特殊的多层链表，可以实现`O(logn)`级别的查找，同时也支持范围查找。实现的基本原理是：在单链表的基础上，向上添加一层索引链表，索引链表有一个指向下一层的指针，可以根据索引链表查到目标对应的区间，然后进行下一步查找，看一张图就好理解了：
>
> ![](https://img2020.cnblogs.com/blog/1774310/202003/1774310-20200307215256429-1534357982.png)
>
> 上图是一个很标准的跳跃表，查找24只需要三次访问，当数据量足够大的时候，跳跃表的查找速度是接近二分查找的。当然跳跃表的实现过程可以有很多种，比较容易的方法是基于随机数的实现方法，每次都采用抛硬币的方法确定某个元素是否需要产生一个上层节点。具体的实现过程可以参照[OI Wiki](https://oi-wiki.org/ds/skiplist/#%E6%8F%92%E5%85%A5)。

# Redis架构

Redis快的三个主要原因：

1. 基于内存的读写，效率高。
2. 单线程架构，省区上下文切换时间。
3. 使用IO多路复用，非阻塞的IO内部使用了epoll+自己实现的简单事件框架。

Redis的整体架构图如下：

![](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/CgotOV2phZSAJMETAACUckqH35I814.png)

## 单线程架构

当第一次看到redis是单线程架构的时候我是十分震惊的，但是redis的单线程并没有影响他的高性能。实际上，redis内部使用了多路复用来处理不同的任务。首先，redis是单线程这是因为redis的核心是一个**事件循环**，这个单线程循环处理所有客户端请求，数据读写，命令解析等任务。redis的单线程架构使用**非阻塞I/O模型**，使用**任务切换**处理事件循环中的不同任务，使用**多路复用技术**监听不同的socket(ip+port)。此外，Redis单线程是针对于redisDB读写而言的，Redis持久化的时候，还是会开启新的线程进行。

### 为什么使用单线程

- 单线程架构不需要考虑锁的问题，在Redis中，有许多的数据结构，每种数据结构在数据量变大的时候锁的细粒度就不好控制，效率可能不升反降。
- Redis的瓶颈在于网络IO而不是CPU，一个PC的CPU就可以在一秒内处理几十万的请求。

> 当然缺点是无法利用多CPU，如果真的需要发挥多CPU的优势，可以启用多个进程。但是瓶颈依然在IO。

### 事件循环

## Redis持久化

- AOF文件（Append-Only File）：用于实现数据的持久化，包含一系列的Redis命令，每个命令都以一种特定的格式写入到文件中，这些命令表示了Redis服务器接收到的写操作，新的命令会不断地追加到文件末尾。当服务器崩溃时，就可以重放AOF文件恢复数据。
- RDB文件（Redis Database）：将数据库某个时间节点的状态保存到一个二进制文件中，相当于备份。如果服务器崩溃，在最后一次生成 RDB 文件和崩溃之间的数据可能会丢失。这时候就需要AOF文件来进一步保护和恢复数据。

看完上述两个持久化的方法之后，我相信善于思考的你对redis持久化已经有了初步的想法了。其基本工作流程基本如下：

`在每一次的写操作中，往AOF文件添加操作记录，当保存了RDB文件之后，就及时清除AOF文件，防止其文件体积过大。当发生崩溃的时候，先加载RDB文件，然后重现AOF文件，最终恢复redis。`这个过程就好像数据库的恢复系统，可以比较记忆。





## Redis的内存模型

