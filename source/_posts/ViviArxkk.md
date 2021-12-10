---
title: 'Go环境变量及常用命令'
date: 2021-10-20 15:52:56
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
### 环境变量

* GOROOT: Go语言的安装路径
* GOPATH: Go语言工作区路径，默认为`~/go`。项目中执行go install命令后，项目构建后生成的可执行程序、静态库文件以及源码文件会被安装到GOPATH相应的目录中去
* GOBIN: 存放Go可执行文件，一般是`GOPATH/bin`会被默认设置到操作系统的PATH环境变量中

```
$ tree -L 1 ~/go
/Users/wangsen/go
├── bin
├── pkg
└── src

$ go env | grep GOPATH
GOPATH="/Users/wangsen/go"

$ go env | grep GOBIN
GOBIN="/Users/wangsen/go/bin"
```

### 常用命令

* `go build`: 构建go程序
* `go test`: 运行测试代码
* `go install`: 构建并安装go程序
* `go fmt`: 格式化go代码
* `go vet`: 用来检查编译器发现不了的逻辑错误
* `go run`: 运行go代码
* `go mod init`: 创建一个新的模块并初始化go.mod文件. `go build`、`go test` 以及其它构建命令会将新的依赖添加到go.mod文件中.
* `go list -m all` 列出模块所有的依赖.
* `go get` 更新或者添加依赖包.
* `go mod tidy`: 删除go.mod中未使用的依赖.

### 关键字

* const、func、import、package、type和var用来声明各种代码元素。
* chan、interface、map和struct用做 一些组合类型的字面表示中。
* break、case、continue、default、 else、fallthrough、for、 goto、if、range、 return、select和switch用在流程控制语句中。 详见基本流程控制语法。
* defer和go也可以看作是流程控制关键字， 但它们有一些特殊的作用。详见协程和延迟函数调用

### 标识符

Go合法标识符由unicode字符、数字及`_`组成，但不能以数字开头，用来表示Go代码元素，如变量、函数、类型以及包名等。
