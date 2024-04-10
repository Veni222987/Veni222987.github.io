---
title: 小白的Kafka笔记
cover_picture: images/cover/mycover.jpg
author: Veni
categories: notes
tags:
  - tag1
date: 2023-11-03 01:04:03
---

Remember to write a excerpt.<!--more-->

# Title

消息队列有RocketMQ，Kafka以及RabbitMQ，本文主要介绍最广泛使用的Kafka。

## 消息、生产者、消费者



## 主题——topic

每一个消费者订阅一个主题，不会造成消息的冲突。主题的集合，也就是Kafka进程叫做broker。多个Broker组成了Kafka集群。可以发现，任何一方都可以支持分布式，可以启用多个实例。卡夫卡的broker在生产者和消费者之间，就像一个集线器。

![image-20231103012812403](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030128600.webp)

## 分区——Partition

一个主题可能包含多个分区，分区可以分布在不同的服务器上。 一个Partition只能由一个Consumer消费，一个Consumer可以消费多个Partition。一般十个左右。注意这个Partition是长期和Consumer连接的，所以只有Consumer数小于Partition数才有意义，不会因为饥饿分配消息。一般设置成10个左右。

![image-20231103012914656](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030129824.webp)

Consumer可以分区、增加减少，会有自动调整。

![image-20231103014553469](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030145646.webp)

- 发送消息时，生产者指定分区则会放到对应的分区，否则由分区器进行分区：
- 所以一条消息包含四个元素

![image-20231103013028344](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030130463.webp)



## 消费者读取——offset

单个partition有一个递增的offset，消费者要定期向broker报告消费的offset。

由偏移量读取数据，一个分区里面，每条消息的偏移量都是必须的，消费者只能**顺序读取**，不然为啥叫消息队列。

![image-20231103013154697](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030131891.webp)

## Kafka集群的主从复制

![image-20231103015151577](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202311030151776.webp)
