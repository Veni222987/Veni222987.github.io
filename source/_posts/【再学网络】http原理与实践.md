---
title: 【再学网络】http原理与实践
date: 2023-08-07 14:27:51
cover_picture: images/cover/notes.jpg
categories: notes
tags:
- http
---

HTTP（Hypertext Transfer Protocol），又叫超文本传输协议，是一种用于在客户端和服务器之间传输超文本的协议。它是互联网上应用最广泛的一种协议，用于在 Web 浏览器和 Web 服务器之间进行通信。<!--more-->

# 【再学网络】HTTP原理与实践

## 为什么需要协议

HTTP是位于OSI参考模型和TCP/IP模型中的应用层的一个工作协议，使用HTTP协议是用户和开发者的双重需要。

从用户的角度看，需要HTTP的原因有：

- 访问和浏览网页：HTTP 协议是 Web 浏览器和 Web 服务器之间进行通信的基础。
- 下载和上传文件：HTTP 协议也被用于文件传输，用户可以通过 HTTP 请求下载文件，或通过 HTTP 请求上传文件。
- 与 Web 应用交互：现代的 Web 应用通常是基于 HTTP 协议构建的，用户可以通过 HTTP 请求与 Web 应用进行交互。

从开发者的角度看，原因有：

- 通信协议的标准化：HTTP 协议定义了客户端和服务器之间通信的规范和约定，使得开发者能够按照统一的标准进行开发。
- 简单和易于开发：HTTP 协议采用简单的文本格式，易于理解和调试。
- 跨平台和互操作性：HTTP 协议是一种跨平台的协议，不依赖于具体的操作系统和开发技术。
- 可扩展和定制化：HTTP 协议支持自定义的头部字段，开发者可以利用这一特性传递额外的信息和参数。



## HTTP协议原理

### HTTP协议内容

![image-20230807151153388](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308071512719.webp)

一个HTTP请求包含请求行、请求头和请求体，如上图中的第一段报文，请求行中的方法名为POST，请求体为邀请小姐姐看电影的话。而一个http响应包含状态行、响应头和响应体三部分，比如上文中的第二段报文。

![image-20230807152944203](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308071529415.webp)

### HTTP请求流程

一个HTTP请求是以客户端的应用层的封装开始，到服务端的应用层的解封装结束。一个HTTP响应则相反，从服务端的封装开始，客户端的解封装结束。一个HTTP请求需要经过中间件，路由和编码解码等层级才能达到服务器。举个简单的例子，一个请求的请求路由是/hello，那么在route层就会对其做一次检验，只有正确的路由才会放行；假设middleware层有一个登录验证的中间件，那么这一层就会把携带不正确身份信息的请求过滤掉。

![ HTTP层次结构图 ](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308071539103.webp)

### 中间件

HTTP 中间件提供了一个方便的机制来过滤进入应用程序的 HTTP 请求，例如，Auth 中间件验证用户的身份，如果用户未通过身份验证，中间件将会把用户导向登录页面，反之，当用户通过了身份验证，中间件将会通过此请求并接着往下执行。当然，除了身份验证之外，中间件也可以被用来运行各式各样的任务，如：CORS 中间件负责替所有即将离开程序的响应加入适当的标头；而日志中间件则可以记录所有传入应用程序的请求。



### 路由

当服务器收到 HTTP 请求时，它会根据请求的 URL 和方法进行路由匹配。如果请求的 URL 和方法与某个路由规则匹配，服务器将调用与之关联的处理程序来处理请求，并生成相应的响应。例如，在一个 Web 应用中，可以定义一个路由规则将 GET 请求的 "/users" 路径与一个处理函数关联起来。当用户在浏览器中访问 "/users" 路径时，服务器将调用该处理函数来处理请求，并返回用户列表。

路由一般会组织成树的结构，也组织成映射结构。一般来说，树状结构可以加快路由匹配速度。



#### 路由参数和请求参数的区别

- 路由参数是指实际的请求路由随着不同的参数值而改变的路由，比如不同的用户登录某个博客网站，博客网站为他们分配了不同的路由，比如`/blog/123,/blog/456`，匹配模式为：`/blog/:id`。



## HTTP实战

下面我们将以Go语言为例，使用Gin框架，将HTTP应用在实战项目中。

### 什么是curl

首先来了解一个工具——curl。curl全称是Client URL，是一个命令行工具和库，用于发送HTTP、HTTPS，FTP等协议的请求，并且获取响应数据。

curl的原理类似于一个命令行式的浏览器，虽然不具备渲染和展现网页的能力，但是具备调试API，下载文件，网络监测等能力。

#### curl的简单使用

- `-X`用于指定发送请求时使用的方法
- `-H`用于指定发送请求的请求头
- `-d`或者`--data`用于指定的数据，可以是文件，JSON，表单等等。

#### 其他发送请求的工具

除了使用CLI的形式发送HTTP请求，还可以使用GUI工具发送。这里推荐使用Postman和APIFox。Postman比较成熟稳定，APIFox偶尔有bug，但具有Postman的功能并且还有写文档和Mock的能力，可以自行取舍。

### 请求方式

#### GET请求

GET请求用于从服务器获取数据。下面以一个简单的获取账户密码的例子来说明GET请求的实现：

首先定义一个实体User，它仅有两个字段：

```go
type User struct {
	Name     string
	Password string
}
```

然后我们测试通过GET传递用户名为参数获取密码：

```go
func GetUserInfo(c *gin.Context) {
	name := c.Query("Name")
	//此处仅作演示，实际开发中不会把密码返回，而是把密码加密存到数据库中
	user := models.User{name, "123456"}
	c.JSON(200, user)
}
```

测试一下：

![image-20230808113646280](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308081136485.webp)

#### POST请求

POST请求用于向服务端推送数据，一般而言，数据写在请求体body中。下面是一个简单的处理POST请求的demo：

```go
func SetUserInfo(c *gin.Context) {
	data, err := io.ReadAll(c.Request.Body)
	if err != nil {
		c.JSON(500, err)
		return
	}
	println("Request body:", string(data))
    //将数据进行处理
	c.JSON(200, gin.H{
		"message": "OK",
	})
}
```

这次使用APIFox代替curl发送请求，这里有几个需要注意的点：

- 请求类型要改成POST请求，相当于指定`-X POST`。
- 请求的信息要写在Body中，不要写在Params中。这一步相当于`-d <json数据>`。

![image-20230819171001584](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308191710697.webp)

后端接收到请求并且打印，输出结果如下：

![image-20230819170459017](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/image-20230819170901529.webp)

#### PUT请求

以下是一个PUT请求示例：

```go
func updateHandler(c *gin.Context) {
	// 从 URL 参数中获取 id
	id := c.Query("id")

	// 解析请求中的 JSON 数据
	var requestData map[string]interface{}
	if err := c.ShouldBindJSON(&requestData); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	// 在这里进行更新操作，例如更新数据库记录等

	// 返回成功响应
	c.JSON(http.StatusOK, gin.H{
		"message": "Resource updated successfully",
		"id":      id,
		"data":    requestData,
	})
}
```

APIFox发送请求以及响应，设置请求同时要设置Params和Body。

![image-20230819174558791](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308191745903.webp)

#### DELETE请求

下面是一个DELETE请求示例：

```go
func deleteHandler(c *gin.Context) {
	// 从 URL 参数中获取 id
	id := c.Query("id")

	// 在这里进行删除操作，例如从数据库中删除记录等

	// 返回成功响应
	c.JSON(http.StatusOK, gin.H{
		"message": "Resource deleted successfully",
		"id":      id,
	})
}
```

APIFox请求和响应结果：

![image-20230819174237717](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308191742816.webp)



#### 比较

当涉及HTTP请求时，GET、POST、PUT和DELETE是最常见的几种方法，用于在客户端和服务器之间传递数据和执行操作。以下是它们之间的比较：

| 维度     | GET请求                  | POST请求                 | PUT请求                | DELETE请求           |
| -------- | ------------------------ | ------------------------ | ---------------------- | -------------------- |
| 用途     | 获取数据，如网页、图片等 | 提交数据，如表单数据     | 更新资源，替换操作     | 删除资源             |
| 幂等性   | 幂等操作，不改变状态     | 非幂等操作，可能改变状态 | 幂等操作，相同更新     | 幂等操作，不改变状态 |
| 数据传递 | 通过URL查询参数传递      | 通过请求体传递数据       | 通过请求体传递完整资源 | 通常通过URL标识资源  |
| 安全性   | 相对较安全，不改变状态   | 相对较安全，可能改变状态 | 相对较安全，需要授权   | 相对较安全，需要授权 |

### 总结

不同的HTTP请求方法在执行操作和传递数据时有不同的语义和适用场景。选择正确的方法取决于业务要执行的操作以及与服务器交互的目的。如希望深入了解HTTP请求方法的内部工作原理和细节，可以研究相关的HTTP协议规范，如RFC 7231。
