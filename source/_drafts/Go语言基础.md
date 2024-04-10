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

# Go语言基础语法

Go语言特点：

- 高性能，高并发
- 丰富的标准库和完整的工具链
- 静态链接
- 快速编译
- 跨平台（各种操作系统，树莓派等）
- 垃圾回收（无需考虑内存分配释放）

## 基础语法

### 变量命名与赋值

- 常量可以不显式指定数据类型，编译器可以根据上下文自动确定

```go
var a = 1
var b int = 2
```

注意！！！在Go语言中赋值有两种方法：=和:=，其中=只是单纯的赋值，而:=表示声明并赋值，如果变量先前已经声明，则可以直接使用=，否则需使用:=。

### 条件控制语句

#### if语句

语法格式如下：

```go
if <bool> {

} else if <bool> {

} else{

}
```

注意，`if`后面的`bool`语句不用打括号，并且`else if` 和`else`必须写在上一个大括号的同一行（？？）

#### for语句

在GO语言中，没有while和do while语句，只有for语句，for语句可以缺省任何一部分值，例如

```GO
for {
    //表示死循环
}
for i:=1;i<10;i++ {
    //从1-9的遍历
}
```

#### switch语句

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

### Map键值对

```go
m := make(map[string]int)
```

### range

在不关心某个切片或者数组索引的时候，可以直接遍历里面的元素，写法如下：

``` go
nums := []int{2, 3, 4}
sum := 0
for _, num := range nums {
    sum += num
}
fmt.Println("sum:", sum)
```

### 函数

```go
//基本形式为func (结构体) <函数名> (函数参数) <返回类型>
//具体看结构体方法部分的代码
```

### 指针

```go
//Go语言的指针使用比较简单，就是C++中的指针传递方法，可以用于在函数中修改指针的值，比如
func test(str *string) string{
    return str+"。"
}
```

### 结构体

```go
//结构体声明
type user struct{
    name string
    psw string
}

//结构体初始化
a:=user(name:"Veni",psw:"1111")
a:=user("Veni","1111")
```

### 结构体方法

类似类成员函数，定义方法：

```go
//非结构体方法的函数定义：
func checkPassword(u user,password string) bool{
    return u.password==password
}

//结构体方法的函数定义
func (u user) checkPassword(password string) bool{
    return u.password==password
}
fmt.Println(a.checkPassword("1111"))//return true or false
```

### 错误处理

在函数返回值的时候可以定义返回值err，同时return两个值，比如

```go
func findUser(users []user,name string)(v *user,err error){
    for _,u :=range users{
        if(u.name==name){
            return &u,nil
        }
    }
    return nil,error.New("Not Found")
}
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



## 输入输出

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

- 格式化扫描输入

```go
// 从 控制台(os.Stdin) 格式化扫描输入
var a int
fmt.Scanf("%d", &a)                         // 输入: 123<回车>
fmt.Println(a)                              // 输出: 123

var b int
var s string
fmt.Scanf("%d%s", &b, &s)                   // 输入: 123 Hello<回车>
fmt.Println(b, s)                           // 输出: 123 Hello

// 从 字符串(string) 格式化扫描输出
fmt.Sscanf("456", "%d", &a)
fmt.Println(a)                              // 输出: 456

fmt.Sscanf("456 World", "%d%s", &b, &s)
fmt.Println(b, s)                           // 输出: 456 World
```

事实上，`%d%s`只是用来格式化的一个占位符，在GO语言中可以使用`%v`代替，没怎么写过C语言的选手也可以用好Scanf啦！

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

字节训练营中的一段bug码，在这段代码中会读取换行符和回车键`\r\n`，需要手动去除。

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



# <center> GO基础小项目

## 猜数字

### 基本原理：

生成随机数，循环输入直至猜对为止，又叫人力二分法。

### 代码

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	maxNum := 100
    //这里需要设置随机数种子，避免每次生成都是一样的结果
	rand.Seed(time.Now().UnixNano())
	secretNumber := rand.Intn(maxNum)
	fmt.Println("The secret num is", secretNumber)
	fmt.Println("请输入你的猜测数字")

	var guess int
	for {
		fmt.Scan(&guess)
		fmt.Println("你猜测的数字是：", guess)
		if guess > secretNumber {
			fmt.Println("大了")
		} else if guess < secretNumber {
			fmt.Println("小了")
		} else {
			fmt.Println("对了")
			break
		}
	}
	fmt.Println("Game Over!")
}
```

## 在线字典

### 基本原理

首先找到一个发送请求，然后使用`convertCurlToGo`将请求转化为Go语言代码，这是模拟前端发送的请求，构建序列化请求。

然后接收返回的json请求，通过一个结构体反序列化，接受结果并输出，生成结构体可以使用`OKTool`里面的`Json转Golang Struct`工具。

### 代码

```go
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
	"log"
	"net/http"
	"os"
)

type DictRequest struct {
	TransType string `json:"trans_type"`
	Source    string `json:"source"`
	UserID    string `json:"user_id"`
}

type DictResponse struct {
	Rc   int `json:"rc"`
	Wiki struct {
	} `json:"wiki"`
	Dictionary struct {
		Prons struct {
			EnUs string `json:"en-us"`
			En   string `json:"en"`
		} `json:"prons"`
		Explanations []string      `json:"explanations"`
		Synonym      []string      `json:"synonym"`
		Antonym      []string      `json:"antonym"`
		WqxExample   [][]string    `json:"wqx_example"`
		Entry        string        `json:"entry"`
		Type         string        `json:"type"`
		Related      []interface{} `json:"related"`
		Source       string        `json:"source"`
	} `json:"dictionary"`
}

func query(word string) {
	//var word string
	//fmt.Println("请输入要翻译的词")
	//fmt.Scan(&word)
	//请求的序列化------------------------------------------------------------
	client := &http.Client{}
	//var data = strings.NewReader(`{"trans_type":"en2zh","source":"good"}`)
	request := DictRequest{TransType: "en2zh", Source: word}
	buf, err := json.Marshal(request)
	if err != nil {
		log.Fatal(err)
	}
	var data = bytes.NewReader(buf)
	req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
	if err != nil {
		log.Fatal(err)
	}
	req.Header.Set("authority", "api.interpreter.caiyunai.com")
	req.Header.Set("accept", "application/json, text/plain, */*")
	req.Header.Set("accept-language", "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6")
	req.Header.Set("app-name", "xy")
	req.Header.Set("content-type", "application/json;charset=UTF-8")
	req.Header.Set("device-id", "b3d49ffbe071b93bbcc3758f5bd40247")
	req.Header.Set("origin", "https://fanyi.caiyunapp.com")
	req.Header.Set("os-type", "web")
	req.Header.Set("os-version", "")
	req.Header.Set("referer", "https://fanyi.caiyunapp.com/")
	req.Header.Set("sec-ch-ua", `"Not/A)Brand";v="99", "Microsoft Edge";v="115", "Chromium";v="115"`)
	req.Header.Set("sec-ch-ua-mobile", "?0")
	req.Header.Set("sec-ch-ua-platform", `"Windows"`)
	req.Header.Set("sec-fetch-dest", "empty")
	req.Header.Set("sec-fetch-mode", "cors")
	req.Header.Set("sec-fetch-site", "cross-site")
	req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.183")
	req.Header.Set("x-authorization", "token:qgemv4jr1y38jyq6vhvi")
	resp, err := client.Do(req)
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	bodyText, err := io.ReadAll(resp.Body)
	if err != nil {
		log.Fatal(err)
	}
	//fmt.Printf("%s\n", bodyText)

	//将返回结果反序列化-----------------------------------------------
	var dictResponse DictResponse
	//注意要加&号
	err = json.Unmarshal(bodyText, &dictResponse)
	if err != nil {
		log.Fatal(err)
	}
	//fmt.Printf("%#v\n", dictResponse)

	//打印结果
	fmt.Println(word, "UK:", dictResponse.Dictionary.Prons.En, "US:", dictResponse.Dictionary.Prons.EnUs)
	for _, item := range dictResponse.Dictionary.Explanations {
		fmt.Println(item)
	}
}

func main() {
	if len(os.Args) != 2 {
		fmt.Fprintf(os.Stderr, `usage:simpleDict WORD example:simpleDict hello`)
	}
	word := os.Args[1]
	query(word)
}
```

## 高质量编程

下面简单说说高质量编程需要注意的一些事情，算是一些经验之谈。

### 高质量编程简介

编写的代码正确可靠，简洁清晰。说白了就是具备安全性、稳定性、简洁性。可以从下面三个角度考量：

- 边界条件：不能出现切片，数组越界的行为，某些特定场景需要考虑变量的物理意义及其范围

- 异常情况：程序运行的时候发生错误需要抛出异常
- 易读易维护：不能写成流水代码

还应该加一点就是代码执行效率，或者说是算法复杂度，比如说能用优先队列的地方就不要每次都遍历插入删除了，在后端开发的过程中性能是很关键的。

### 代码规范

代码规范主要包括代码格式，注释，命名规范，控制流程，错误和异常处理的标准。

- go fmt自动格式化代码，go imports自动增删依赖包引用，将依赖包按照首字母排序
- 注释：公共符号始终需要注释，不需要注释  实现接口的方法，接口是给别人用的，实现接口的方法是自己看的。
- 命名：缩略词全大写，但位于变量开头且不需要导出的时候，使用全小写。
- 命名规范：注意包名和函数名的关系，最好的方式是换位思考，比如你自己写了一个myHTTP包，里面写了一个函数myHTTP.ServeHTTP，自己写和看都不顺眼就知道怎么改了。
- 控制流程，避免嵌套，如果两个分支都包含return语句，可以去掉一个。
- 优先处理错误和特殊情况，尽早返回可以减少嵌套。
- 错误和异常处理：panic，recover和error。若问题可以被屏蔽和解决可以使用error，main函数可以使用panic。三者区别：error尽可能提供简明的上下文信息链，方便定位问题；panic用于真正异常的情况；recover在当前goroutine的被defer的函数中生效，即恢复到错误发生的上一个函数。

### 性能优化建议

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
