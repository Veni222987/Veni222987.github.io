---
title: Go语言常用命令
date: 2023-07-30 21:05:13
categories: go
cover: https://tse2-mm.cn.bing.net/th/id/OIP-C.pL59BikVm-D_YjBfbgSMagHaEW?rs=1&pid=ImgDetMain
tags:
- 后端
- go
toc: true
mathjax: true
author: Veni
---

Go语言自带有一整套完整的命令操作指令，可以在命令行中执行`go`查看所有指令。<!-- more-->比如：

```bash
C:\Users\19481>go
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies
        clean       remove object files and cached files
        doc         show documentation for package or symbol
        env         print Go environment information
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources
        generate    generate Go files by processing source
        get         add dependencies to current module and install them
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        work        workspace maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages
```

这里只截取指令部分的展示结果，将选取其中最常用的几个指令，做一次系统的整理与回顾。

# Go语言常用命令

## go run

编译并运行go程序。使用`go run`命令运行Go程序时，编译的结果并不会被保存在磁盘上。而是将源代码编译为二进制文件，并直接在内存中执行。可以说，刷OJ最常用就是他了。

## go test

go test指令最常用于单元测试，使用方法如下：

- 确保测试程序以_test.go结尾，这是Go语言的约定形式，别问为啥。
- 在终端中使用以下命令运行测试：

```
go test -run TestFunctionName
```

其中，`TestFunctionName`是你要运行的测试函数的名称。注意，测试函数的名称必须以`Test`开头，且函数签名必须符合以下形式：

```go
func TestFunctionName(t *testing.T) {
    // 测试逻辑
}
```

- 运行测试后，你将在终端中看到测试结果的输出，包括通过的测试用例数量、失败的测试用例数量以及测试的覆盖率等信息。

如不指定测试运行的程序，那么go test就会把当前目录下所有的_test.go程序都执行一遍。

## go build

这个命令用于测试编译，在包的编译过程中，若有需要，则会同时编译与之相关的包。下面用一个简单的程序测试一下，具体语法是：

```bash
go build <go源代码>
```

比如：

```shell
PS D:\Desktop\MyProjects\ByteTech> go build Course1.go
PS D:\Desktop\MyProjects\ByteTech> ls
    目录: D:\Desktop\MyProjects\ByteTech
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2023/7/30     10:57                .idea
-a----         2023/7/30     10:57        2159104 Course1.exe
-a----         2023/7/25     22:25            528 Course1.go
-a----         2023/7/25      9:50             25 go.mod
```

命令执行完成之后，会在原目录下生成一个同名的可执行文件，双击即可运行程序。这个过程就好像使用g++编译C++源代码。当然你也可以用`go build -o <文件名>`指定生成的文件类型和路径。如果`go build`之后不接任何参数，则该命令会默认编译当前目录下所有的go文件。

- `go build`会忽略目录下以'_'和'.'开头的文件。

## go install

`go install`命令做了两件事情，一件是把源代码编译成为结果文件（可执行文件或.a包），然后将其放到了`$GOPATH`里面的bin或pkg目录下。

## go clean

这个命令用来清除当前源码包里面编译生成的一些文件，比如go build和go test生成的.exe文件等等。

```shell
PS D:\Desktop\MyProjects\ByteTech> go clean
PS D:\Desktop\MyProjects\ByteTech> ls
    目录: D:\Desktop\MyProjects\ByteTech
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2023/7/30     10:57                .idea
-a----         2023/7/25     22:25            528 Course1.go
-a----         2023/7/25      9:50             25 go.mod

```

可以看到，刚才生成的exe文件已经被清除掉了。这个命令可以用在远程仓库管理的时候，一般来说github上面只存代码，不存编译结果，所以可以先执行`go clean`之后在执行`git push`命令。要是我的话直接用`gitignore`比这个方便多了。

## go fmt

这个命令是用来格式化go语言代码的，一些格式错误可能会导致过不了编译，比如else要跟在上一个右大括号后面，左大括号必须跟在上一行的结尾等等。好在一般的IDE都会在保存的时候帮我们执行这条指令。要是使用vim或者记事本编辑的话可就得手动执行这条指令了。



## go get

这个命令是用来动态获取远程代码包或第三方依赖库的，支持了GitHub,BitBucket,Google Code等，使用的时候需要下载相对应的工具（比如git）。使用的方法如下：

```bash
go get github.com/gin-gonic/gin
```

使用go get命令下载第三方依赖的时候，下载的依赖包会放置在$GOPATH/src目录下。



## go env

### 查看设置

`go env`用于设置和查看go语言的环境，其中直接执行`go env`可以查看当前机器的GOPATH，GOPROXY之类的设置，执行结果如下：

```
PS D:\Desktop> go env
set GO111MODULE=on
set GOARCH=amd64
...
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -Wl,--no-gc-sections -fmessage-length=0 -fdebug-prefix-map=C:\Users\19481\AppData\Local\Temp\go-build1674882432=/tmp/go-build -gno-record-gcc-switches
```

### go env设置代理

大家使用`go get`下载包的时候，可能会遇到无法连接等情况，这时候可以把GOPROXY设置为国内的阿里云镜像：

```
go env -w GO111MODULE=on
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct
```

其中`GO111MODULE`是一个用于控制Go模块支持的环境变量，待选值为：on, off, auto。

- "off"：禁用Go模块支持。在这种模式下，Go将继续使用旧的GOPATH和vendor目录来管理依赖关系。
- "on"：启用Go模块支持。在这种模式下，Go将使用go.mod和go.sum文件来管理依赖关系，并将依赖项下载到模块缓存中。
- "auto"：根据当前工作目录下是否存在go.mod文件来决定是否启用Go模块支持。如果存在go.mod文件，则启用模块支持；否则，禁用模块支持。

另外一个GOPROXY后面的值是阿里云镜像的地址。-w是writable，表明该变量是可写的。



## go mod

go mod是Go语言中对于第三方库和插件的依赖管理工具。使用go mod可以方便地管理Go语言的各种依赖，下面将展示如何使用go mod管理依赖。

### go mod init

`go mod init`指令用于初始化go.mod文件，该指令会在工程根目录下创建一个go.mod文件，一个go.mod文件的常见结构如下：

```
module GinusThaiBeauty

go 1.20

require (
	//这里写你要下载的依赖
	github.com/gin-gonic/gin v1.9.1
)
```



### go mod download

使用`go mod download`命令的时候，依赖库会被下载到全局的Go Modules缓存中，而非项目的GOPATH。在项目配置中必须要勾选`Index entire GOPATH`才会扫描全局Go Modules。



### go mod vendor

在1.11版本之前，Go语言的主要依赖管理工具是go vendor。Golang 1.11之后，由于引入了Module功能，在运行go build时，优先引用的是Module依赖包的逻辑，所以Vendor目录就被“无视”了，进而可能发生编译错误， moudle 说还是很想他，于是提供了 go mod vendor命令用来生成 vendor目录。



## 其他

其他的指令使用频率相对较低，比如`go doc`（官方文档），`go version`（版本号），需要的时候运行一下`go help <指令>`即可。
