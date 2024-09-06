---
title: Go语言基础语法
date: 2023-08-02 14:52:04
categories: go
cover: https://tse2-mm.cn.bing.net/th/id/OIP-C.pL59BikVm-D_YjBfbgSMagHaEW?rs=1&pid=ImgDetMain
tags:
- go
- 笔记
---

Go语言（也称为Golang）是一门由Google开发的开源编程语言。Go语言的设计目标是提供一种简单、高效、可靠的编程语言，适用于大规模系统开发。<!-- more -->

# 语法

Go语言特点：

- 高性能，高并发
- 丰富的标准库和完整的工具链
- 静态链接
- 快速编译
- 跨平台（各种操作系统，树莓派等）
- 垃圾回收（无需考虑内存分配释放）

## 基础语法

过于基础的for if else在这里就不介绍了，这里主要讲一些有特点的语法。

### 数组与切片

#### 数组

数组长度是固定的，具有内置的操作函数。而且数组在函数传递的时候传递的方式是值传递。

```go
var arr [5]int
fmt.Println(arr[0],len(a))
```

#### 切片

有点类似C++的Vector，但不是一个对象。len和append函数不能直接使用'.'获取，而是以函数的形式封装在标准包里面。切片是长度可变的数组，用法如下：

```go
s := make([]string,3)
fmt.Println(s[0],len(s));
s=append(s,'f')

//选择数组长度的方法
fmt.Println(s[1:5])
fmt.Println(s[:5])
fmt.Println(s[1:])
```

切片是go中最常用的数据结构，具有两个重要的属性：长度和容量，直接使用 len获取的是

### channel



### switch和select语句

```go
switch a{
    case 1:
    case 2:
    default:
}
```

GO语句在switch之后不需要加break手动破坏，即每次只会执行一个结果。

switch可以使用任意的数据结构，比如

```go
t := time.Now()
switch {
    case t.Hour()<12:
    default:
}
```

select语句是专门针对channel的，

### 数组与切片

#### 数组

数组长度是固定的，具有内置的操作函数。

```go
var arr [5]int
fmt.Println(arr[0],len(a))
```

#### 切片

有点类似Vector，但是不是一个对象len和append函数不能直接使用'.'获取，长度可变的数组，实现方法如下

```go
s := make([]string,3)
fmt.Println(s[0],len(s));
s=append(s,'f')

//选择数组长度的方法
fmt.Println(s[1:5])
fmt.Println(s[:5])
fmt.Println(s[1:])
```

### 字符串函数

OJ必备，用好了可以提高效率

#### 字符串操作函数

```go
import{
    "strings"
}

func main(){
    a:="hello"
    strings.Contains(a,"ll")//true
    strings.Count(a,"l") //2
    strings.HasPrefix(a,"he")//true
    strings.HasSuffix(a."llo")//true
    strings.Index(a,"ll")//2
    strings.Repeate(a,2)//hellohello
    strings.Replace(a,"e","E",-1)//hEllo
    strings.Split("a-b-c","-")//[a b c]
    strings.ToLower(a)//hello
    strings.ToUpper(a)//HELLO
}
```

#### 字符串格式化

```go
fmt.Printf("s=%v\n",p) //%v可以是任意类型的占位符
fmt.Printf("s=%+v\n",p)
fmt.Printf("s=%#v\n",p) //更加详细的信息
fmt.Printf("s=%.2f\n",3.1415)
```

#### 数字解析

引入strconv包

```go
n,_ :=strconv.ParseInt("111",10,64)

n1,_ :=strconv.Atoi("111")
```

### Json操作

只需要定义一个结构体，结构体的首字母需要大写，之后就可以直接进行序列化。

```go
type userInfo struct{
    Name string
    Age int `json:"age"` //将字段名改成小写的age，一般不加这一段
    Hobby []string
}

//序列化
buf,err :=json.Marshal(a)
jmt.Println(string(buf)) //打印json，记得加string，不然会打印出一堆十六进制编码

//反序列化
var b userInfo
err=json.Unmarshal(buf,&b)
```

### 时间处理

需要`import time`，可以直接查文档

```
t:=time.Now()
```

### 进程信息

使用GO语言可以查看进程的相关信息。



## 标准输入输出

### 输入的几种方法

#### 使用fmt包

- 格式化扫描输入

```go
// 从 控制台(os.Stdin) 扫描输入
var a int
fmt.Scan(&a)                                // 输入: 123<回车>
fmt.Println(a)                              // 输出: 123

var b int
var s string
fmt.Scan(&b, &s)                            // 输入: 123 Hello<回车>
fmt.Println(b, s)                           // 输出: 123 Hello

// 从 字符串(string) 扫描输出
fmt.Sscan("456", &a)
fmt.Println(a)                              // 输出: 456

fmt.Sscan("456 World", &b, &s)
fmt.Println(b, s)                           // 输出: 456 World
```

#### 从标准输入(`os.Stdin`)直接读取数据

```go
// 创建 字节切片 用于保存读取到的字节序列
buf := make([]byte, 10)

// 等待从 控制台(os.Stdin) 输入数据, 按回车键触发读取,
// 最多读取 len(buf) 个字节, 
// 回车符(\n)也算作是输入的数据, 也会被读取,
// 读取的字节序列是经过 UTF-8 编码的字节序列,
// 返回的 n 表示本次读取的字节数
n, err := os.Stdin.Read(buf)
fmt.Println(n, err)

// 转换为字符串输出
fmt.Printf("%s", string(buf[:n]))

```

#### 使用`bufio.Scanner`扫描器读取数据

```go
// 创建一个扫描器对象, 从 os.Stdin 中扫描数据
scan := bufio.NewScanner(os.Stdin)

// 阻塞等待输入, 输入数据后, 按回车触发扫描, Scan() 返回 true
if scan.Scan() {
    // 以 (UTF-8)字符串 的形式读取本次扫描的数据 (支持输入空格, 不包括换行符, 换行符自动舍去)
    s := scan.Text()
    fmt.Printf("%s\n", s)
}

// 继续阻塞等待下次输入
if scan.Scan() {
    // 以 (UTF-8)字节切片 的形式读取本次扫描的数据
    b := scan.Bytes()
    fmt.Printf("%s\n", string(b))
}
```

一段bug码，在这段代码中会读取换行符和回车键`\r\n`，需要手动去除。

```go
reader := bufio.NewReader(os.Stdin)
input, err := reader.ReadString('\n')
if err != nil {
	fmt.Println("输入错误，请重新输入")
	return
}
input = strings.Trim(input, "\r")
input = strings.Trim(input, "\n")
```



# 高质量编程

下面简单说说高质量编程需要注意的一些事情，算是一些经验之谈。

## 高质量编程简介

编写的代码正确可靠，简洁清晰。说白了就是具备安全性、稳定性、简洁性。可以从下面三个角度考量：

- 边界条件：不能出现切片，数组越界的行为，某些特定场景需要考虑变量的物理意义及其范围

- 异常情况：程序运行的时候发生错误需要抛出异常
- 易读易维护：不能写成流水代码

还应该加一点就是代码执行效率，或者说是算法复杂度，比如说能用优先队列的地方就不要每次都遍历插入删除了，在后端开发的过程中性能是很关键的。

## 代码规范

代码规范主要包括代码格式，注释，命名规范，控制流程，错误和异常处理的标准。

- go fmt自动格式化代码，go imports自动增删依赖包引用，将依赖包按照首字母排序
- 注释：公共符号始终需要注释，不需要注释  实现接口的方法，接口是给别人用的，实现接口的方法是自己看的。
- 命名：缩略词全大写，但位于变量开头且不需要导出的时候，使用全小写。
- 命名规范：注意包名和函数名的关系，最好的方式是换位思考，比如你自己写了一个myHTTP包，里面写了一个函数myHTTP.ServeHTTP，自己写和看都不顺眼就知道怎么改了。
- 控制流程，避免嵌套，如果两个分支都包含return语句，可以去掉一个。
- 优先处理错误和特殊情况，尽早返回可以减少嵌套。
- 错误和异常处理：panic，recover和error。若问题可以被屏蔽和解决可以使用error，main函数可以使用panic。三者区别：error尽可能提供简明的上下文信息链，方便定位问题；panic用于真正异常的情况；recover在当前goroutine的被defer的函数中生效，即恢复到错误发生的上一个函数。

## 性能优化

#### 使用Benchmark检查性能

`benchmark` 命令通常是指在编程语言和开发工具中用于性能测试和性能测量的一类特殊命令或工具。这些命令或工具允许开发者评估代码的性能，找出潜在的性能问题，进行性能优化，以及比较不同实现之间的性能差异。

在test时使用Benchmark，指令：

```bash
go test -bench=. -benchmem
```

#### Slice调优

创建slice的时候预分配内存，使用make初始化的时候提供内存信息。除了Go，所有的语言可变长度的数组（容器）都应该预分配内存，比如matlab，C++的Vecotr，改变容量需要重新申请内存，影响效率。

#### Map调优

预分配内存，与Slice做法类似。

#### 字符串处理

+号，strings.Builder处理方法，推荐使用strings.Builder或strings.Buffer方法

#### 空结构体

顾名思义就是什么成员都没有的结构体，由于其不占内存空间并且具有一定的语义，比如直接返回一个空结构体代替异常。
