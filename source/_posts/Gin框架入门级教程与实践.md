---
title: Gin框架入门级教程与实践
date: 2023-08-05 22:40:28
cover: https://tse2-mm.cn.bing.net/th/id/OIP-C.pL59BikVm-D_YjBfbgSMagHaEW?rs=1&pid=ImgDetMain
categories: go
tags:
- go
- 后端
---

Gin是一个用Go语言编写的高性能Web框架，它简洁、快速，并具有良好的路由和中间件支持。<!--more-->

# Gin框架入门级教程与实践

## 为什么使用Gin

我们使用Gin的原因很直接也很简单，那就是Gin很强大也很简单。Gin框架具有如下的有点：

- **高性能**：Gin框架使用了基于Radix树的路由匹配算法，使得路由查找非常快速。同时，Gin框架基于HTTP标准库进行开发，具有低内存占用和高并发处理能力。
- **简单易用**：Gin框架提供了简洁的API和易于理解的代码结构，使得开发者能够快速上手并编写清晰、可维护的代码。
- **路由和中间件支持**：Gin框架提供了灵活的路由定义方式，支持参数路由、分组路由等。同时，Gin框架内置了丰富的中间件支持，如日志、认证、CORS等，可以方便地实现各种功能和扩展需求。
- **JSON解析和输出**：Gin框架内置了强大的JSON解析和输出功能，可以方便地处理请求数据和生成响应。同时，Gin框架还支持其他格式的数据解析和输出，如XML、YAML等。
- **插件生态丰富**：Gin框架有着活跃的社区和丰富的插件生态，可以方便地集成其他常用库和组件，如数据库ORM、缓存库、验证库等。

这些看一下就好，以后会体会到Go的高并发和JSON解析等强大功能的。

## Gin框架的入门

### 下载Gin依赖

执行以下命令，下载和安装Gin依赖：

```bash
go get -u github.com/gin-gonic/gin
```

以下指令会把Gin安装在GOPATH里面的src文件夹中。

### 第一个Gin应用

我们先写一个Gin应用，打开Gin的世界。新建一个main.go文件，敲进去这样的代码：

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()
	r.GET("/hello", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "Veni",
		})
	})
	r.Run(":8888")
}
```

然后运行一下：

```bash
go run main.go
```

打开浏览器，输入：`localhost:8888/hello`，结果返回为`"message":"Veni"`，第一个Gin应用完美运行。

![image-20230807112709050](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308071127195.webp)

#### 代码解析

package和import就不说了，从main函数开始。

```go
r := gin.Default()
```

这一段代码用来创建一个默认的Gin引擎的实例并赋值给r，该实例已经配置了一些常用的中间件和默认的设置，比如默认使用Logger中间件用于记录请求日志，默认使用Recovery中间件用于恢复panic。还有默认的错误信息处理，默认请求路由器等等。

```go
r.GET("/hello", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "Veni",
		})
	})
```

r.GET()用于创建一个GET请求的处理函数，请求的路由是"/hello"，然后绑定了一个处理函数func。func函数的参数是`c *gin.Context`，这是Gin框架里面的上下文对象，它封装了每个http请求的信息和操作方法，用于处理请求和响应。为什么要使用指针`*`呢？因为这里面需要对这个上下文进行修改，因此采用指针传递才能在函数中修改变量的值。Gin.Context有许多用法，比如：

- `c.Request`：获取原始的 http.Request 对象。
- `c.Writer`：获取原始的 http.ResponseWriter 对象。
- `c.Param(key)`：获取路由参数值。
- `c.Query(key)`：获取查询参数值。
- `c.PostForm(key)`：获取表单参数值。
- `c.JSON(code int, obj interface{})`：以 JSON 格式返回数据。
- `c.HTML(code int, name string, data interface{})`：渲染 HTML 模板并返回。
- `c.Set(key string, value interface{})`：设置上下文的键值对。
- `c.Get(key string) interface{}`：获取上下文中的值。

所以，上面的c.JSON的作用就是以JSON格式返回数据，函数的第一个参数是整形返回码，interface可以是任意类型的对象。

gin.H{}是Gin框架中用来创建JSON或HTML相应的方式，可以创建一个 map [string] interface类型的对象。使用方法如下：

```go
data := gin.H{
			"message": "Hello, World!",
			"count":   10,
			"success": true,
		}
c.JSON(200, data)
```

在实际开发中，一般的返回JSON都命名成data，符合前后端开发的习惯。

### Gin项目结构

Gin的项目结构可以是因人而异的。Go语言相互调用包的过程很简单，公有变量和私有变量只需要使用大小写区分。受到MVC架构的影响，我一般将项目组织成如下的结构：

```
- main.go  
- router.go
- config.yaml
- controller/
  - user.go
- model/
  - user.go
- middleware/
  - authMiddleware.go
- service/
  - database.go
  - redis.go
- test/
  - config.yaml
  - main_test.go
  - user_test.go
- utils/
  - time.go
```

- main文件是程序的入口，用于完成初始化，启动Gin等操作；router用于配置路由信息，包括对应路由的处理函数。
- controller目录为控制器层，用于处理路由对应的请求。
- model目录是模型层，对应着数据库的一个关系或者自建的实体模型。
- middleware是中间件层，用于完成权限认证等操作。
- service层存放的是业务逻辑的相关代码，以及调用其他服务或API的代码，可以将DB和Redis等操作代码也放在这一文件夹中。
- test是测试目录，用于完成单元测试等工作。

在项目的实际开发中，推荐使用的命名方法是：公有类型名和方法名使用大驼峰，私有类型和方法、目录和文件名使用小驼峰。

## Gin项目结构逐层解析

现在你已经能够启动Gin框架的hello world，并且也了解了Gin的项目结构。下面我们将逐个实现上述结构，让萌新也可以拥有一个完整的项目框架经验。

### package main

#### main.go

前文中我们直接在main函数处理了一个GET请求，但是实际开发中是绝对不会把处理函数写在main函数里面的。main函数主要做两件事情：初始化环境和启动Gin。所以，一个简单的main.go文件如下：

```go
func main() {
    //初始化引擎
	r := gin.Default()
	//初始化路由器
	InitRouter(r)
    //初始化数据库
	service.InitDatabase()
    //初始化Redis
	service.InitRedis()
	//启动Gin
	r.Run(":8888")
}
```

#### router.go

初始化引擎一句之前已经解析过了，下面讲讲InitRouter(r)。这个函数我们写在router.go这个文件中，用于处理路由和相关请求。比如下面的这个文件：

```go
func InitRouter(r *gin.Engine) {
    //静态文件路径
    r.Static("/static", "./public")
    //注册路由组
    router := r.Group("/api")
    //在路由组下注册路由
    router.POST("/user/login/", controller.Login)
}
```

- 函数的参数需要带指针*，不然在主函数中调用是不会改变r的值的。
- r.Static用于注册静态文件的路径，比如某些HTML以及图片等，将其指向本地的public文件夹。
- 使用r.Group注册路由组，使用路由组可以统一注册中间件，同时也符合树形结构路由的要求。
- router.POST则指定了一个路径下的POST请求及其处理函数（controller.Login）。此时的路径必须加上路由组的路径，即完整的路径为：`/api/user/login/`。

#### config.yaml

该文件用于处理所有重要的配置，比如数据库，Redis等等。使用yaml记录配置十分简洁，可以将字段一一对应到某个go语言的结构体中，内容如下：

```yaml
database:
  driver:
  host:
  port:
  username:
  password:
  database:

redis:
  addr:
  password:
  DB:

OSS:
  endPoint:
  accessKey:
  accessSecret:
```

每一个层级对应一个结构体，最小的层级对应结构体的一个字段。比如，用来存储database配置的两个结构体如下：

```go
type DatabaseConfig struct {
	Driver   string `yaml:"driver"`
	Host     string `yaml:"host"`
	Port     int    `yaml:"port"`
	Username string `yaml:"username"`
	Password string `yaml:"password"`
	Database string `yaml:"database"`
}

var config struct {
	Database DatabaseConfig `yaml:"database"`
}
```

### package service

在主函数里面有一个初始化数据库的函数我们没有说，现在我们来讲一讲这个`service.InitDatabase()`。这里需要一点gorm的基础，可以去看[我的博客另一篇介绍gorm的文章](https://veni222987.github.io/2023/08/10/%EF%BC%88%E5%AE%8C%EF%BC%89GORM%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%E4%B8%8E%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5/)。具体实现代码如下：

```go
var Db *gorm.DB
func InitDatabase() {
    //读取配置文件
	configFile, err := os.ReadFile("config.yaml")
	if err != nil {
		log.Fatal(err)
	}
    //反序列化configFile到config（即上面的var config struct{}变量）
	err = yaml.Unmarshal(configFile, &config)
	if err != nil {
		log.Fatal(err)
	}
	//拼接字符串
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8mb4&parseTime=True&loc=Local",
		config.Database.Username,
		config.Database.Password,
		config.Database.Host,
		config.Database.Port,
		config.Database.Database,
	)
	Db, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
}
```

DSN(Database Source Name)为数据库资源名称，用于gorm打开数据库并建立连接。后面对数据库的所有操作都可以通过Db这个变量来完成。

### package model

这个目录存储了主要的结构体，比如`model/user.go`：

```go
type User struct {
	Id              int64  `gorm:"id" json:"id"`                             // 用户id
	Name            string `gorm:"name" json:"name"`                         // 用户名称
	FollowCount     int    `gorm:"follow_count" json:"follow_count"`         // 关注总数
	FollowerCount   int    `gorm:"follower_count" json:"follower_count"`     // 粉丝总数
	Avatar          string `gorm:"avatar" json:"avatar"`                     // 用户头像
	BackgroundImage string `gorm:"background_image" json:"background_image"` // 用户个人页顶部大图
	Signature       string `gorm:"signature" json:"signature"`               // 个人简介
	TotalFavorited  int    `gorm:"total_favorited" json:"total_favorited"`   // 获赞数量
	WorkCount       int    `gorm:"work_count" json:"work_count"`             // 作品数
	FavoriteCount   int    `gorm:"favorite_count" json:"favorite_count"`     // 喜欢数
}
//绑定数据库表名，如不指定，则默认为蛇形复数
func (*User) TableName() string {
	return "user"
}
```

使用gorm可以指定某一个属性对应的数据库表的字段，使用json则可以指定当该结构体序列化为json的时候的字段名。

### package controller

下面是一个控制器的实例，用于处理路由器中已经指定的路由及其处理函数。这里以login为例，函数的具体实现如下，该函数写于`controller/user.go`文件中

```go
// 登录功能
func Login(ctx *gin.Context) {
	DB := service.Db
	//获取参数
	name := ctx.Query("username")
	password := ctx.Query("password")
	//判断用户是否存在
	account := model.Account{}
	DB.Table("account").Where("username = ?", name).Find(&account)
	if len(account.Username) == 0 {
		ctx.JSON(http.StatusUnprocessableEntity, gin.H{"code": 422, "msg": "用户不存在"})
		return
	}
	//判断密码是否正确
	if err := bcrypt.CompareHashAndPassword([]byte(account.Password), []byte(password)); err != nil {
		ctx.JSON(http.StatusBadRequest, gin.H{"code": 400, "msg": "密码错误"})
		return
	}
    //......

	//返回结果
	ctx.JSON(200, gin.H{
		"status_code": 0,
		"status_msg":  "string",
	})
}
```

### package middleware

这个包用来存放中间件的相关代码，简单的token验证中间件的实现方法如下：

```go
func QueryAuthMiddleWare() gin.HandlerFunc {
	return func(ctx *gin.Context) {
		token := ctx.Query("token")
		if service.IsTokenExist(token) {
			//fmt.Println("鉴权成功，token有效\n")
			service.RedisClient.Set(token, service.RedisClient.Get(token).Result, 86400000000000)
			ctx.Next()
		} else {
			fmt.Println("无效的token")
			ctx.AbortWithStatusJSON(401, gin.H{
				"error": "无效的Token",
			})
			return
		}
	}
}
```

这里假设处理函数面对的HTTP请求把token放在了Query的位置，则通过ctx.Query("token")即可获取到这个token值。这里使用Redis查询是否存在该token从而完成权限鉴定功能，具体实现在函数`service.IsTokenExist(token)`中，这里就不给出详细代码了，可以自行到github仓库里面看。

### package utils

这里存放一些代码中可能会使用到的工具函数，比如日期，时间转换等等。下面是一个日期转换的工具：

```go
type CustomTime struct {
	time.Time
}

func (ct *CustomTime) UnmarshalJSON(b []byte) (err error) {
	s := string(b)
	if s == "null" {
		ct.Time = time.Time{}
		return
	}

	s = s[1 : len(s)-1]
	t, err := time.Parse("2006-01-02 15:04:05", s)
	if err != nil {
		return err
	}
	ct.Time = t
	return
}

func (ct *CustomTime) MarshalJSON() ([]byte, error) {
	if ct.IsZero() {
		return []byte("null"), nil
	}
	return []byte(fmt.Sprintf("\"%s\"", ct.Format("2006-01-02 15:04:05"))), nil
}
```

这个函数可以把日期序列化为`YYYY-MM-DD hh-mm-ss`的形式。

### package test

#### 单元测试

这个包里面的函数用于开展单元测试，单元测试是软件开发中的一种测试方法，旨在验证代码中的最小可测试单元（通常是函数、方法或类）是否按预期工作。

- 对于需要测试的代码文件创建一个名为`*_test.go`的文件。
- 测试代码写成函数形式：`func TestXxx(t *testing.T)`
- 初始化逻辑放在`TestMain(m *testing.M)`函数中，并且文件取名为main_test.go

#### 测试文件

在test目录下，一般需要包含如下文件：config.yaml，main_test.go，\*_test.go（\*代表任意匹配），config.yaml里面的配置填写测试环境配置，比如测试数据库。main_test.go完成对测试环境的初始化工作，比如：

```go
//main_test.go
func TestMain(m *testing.M) {
	service.InitRedis()
	service.InitDatabase()
	code := m.Run()
	os.Exit(code)
}
```

然后就可以一次写各个函数的单元测试函数了，下面是一个处理gin框架下的POST请求的一个单元测试函数：

```go
//user_test.go
func TestLogin(t *testing.T) {
	router := gin.New()
	router.POST("/douyin/user/login", controller.Login)

	// 创建一个模拟的HTTP请求
	req, _ := http.NewRequest("POST", "/douyin/user/login?username=Veni&password=asdfghjkl", nil)
	resp := httptest.NewRecorder()

	// 将请求发送到路由引擎处理
	router.ServeHTTP(resp, req)

	// 验证响应
	if resp.Code != 200 {
		t.Errorf("Expected status code 200, but got %d", resp.Code)
	}
}
```

#### 启动测试

由于本项目将`main_test.go`文件放在了test包中，那么就不能直接运行go test指令来启动测试了，取而代之的是go test ./test，此时test目录相当于一个项目的根目录，会运行`main_test.go`之后自动扫描其他的test文件中的测试函数，一次运行的结果如下：

![image-20230824182624455](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308241826655.webp)

这里返回了422编码，表明用户不存在，因为测试数据库中并没有这个用户记录。

## 总结

以上就是本次Gin框架的基础教程了，部分代码截自[我的第一个go项目](https://github.com/Veni222987/DoushengABCD.git)，大家可以到仓库里面看具体代码，如果对大家有帮助，不妨在github点个star或者给这篇文章点个赞。

