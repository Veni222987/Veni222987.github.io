---
title: 【Exp】MySQL索引那些事儿
tags:
  - MySQL
  - RDBMS
categories: Exp
cover: /img/mycover.jpg
highlight_shrink: false
date: 2024-03-17 21:42:36
---

# 索引简介

## 什么是索引

还是先来说一下什么是索引吧。Wiki的描述如下，通俗地讲，索引就是加速数据库访问的一种数据结构。

> A **database index** is a [data structure](https://en.wikipedia.org/wiki/Data_structure) that improves the speed of data retrieval operations on a [database table](https://en.wikipedia.org/wiki/Table_(database)) at the cost of additional writes and storage space to maintain the index data structure. 

## 索引实现原理
