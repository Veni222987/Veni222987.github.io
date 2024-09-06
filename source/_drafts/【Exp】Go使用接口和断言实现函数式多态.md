---
title: 【Exp】Go使用接口和断言实现函数式多态
tags:
  - Go
categories: Exp
cover: /img/mycover.jpg
highlight_shrink: false
date: 2024-04-20 10:02:05
---

# 接口入门

接口是Go四大类型之一：基础类型、聚合类型、引用类型和接口类型。可以这么说，前面三个类型决定了Go语言的基本使用需求，接口类型保证了Go的可扩展性。合理利用接口可以降低一个项目中各个模块的耦合性，使系统架构更加健壮和优美。

## 定义接口

前面我们说了，接口是一种类型，因此定义接口就是重新定义一种类型。其实结构体和接口有点类似，结构体由结构名和若干字段名组成，接口由接口名和若干函数签名组成，声明语法很相似，甚至连读音都很相似，不信可以多读两次`接口结构`试下。

```go
// 定义结构
type Person struct {
	name string
	age  int
}

// 定义接口
type Formatter interface {
  MyWrite(strs ...string) (res string, err error)
  MyRead(strs ...string) (res string, err error)
}
```

注意几个关键点：

1. 每个函数签名之间不需要分隔符号（如逗号、分号），后面也不需要大括号
2. 函数签名之前不需要func关键字，不然就是在定义函数而不是接口了。

## 为什么说接口是隐式实现的

## 实现和使用接口

在Go中，接口的实现是`隐式的`。什么意思呢？
