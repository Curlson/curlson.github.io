---
title: go module 摆脱 GOPATH 的包管理束缚吧！
date: 2019-10-20 23:04:42
tags: 
 - Go
 - 包管理工具
 - Go Module
 
categories:
 - Go
---

​	最近被 `go get` 折腾的身不如思，下载墙外的包的时候。就有种段誉使用六脉神剑的感觉，你急它不急，时灵时不灵。终于，找到了 `go module` 用起来很顺手，从1.11版本开始就已经可以采用 `go module` 来进行扩展包的管理了，可以让你摆脱 `GOPATH` 的束缚。

<!-- more -->

查看 `Go` 和是否开启 `go module`版本

```go
// 1. 查看 go 版本
go version
// 输出当前 go 的版本，版本大于 1.11 可以使用 go module
go version go1.12.6 windows/amd64

// 2. 默认是，go将根据当前目录来决定是否启用 module 功能。这种情况下分为两种情形：
// 当前目录在 GOPATH/src 之外且该目录包含 go.mod 文件，则使用 go module 。
```

创建测试项目，并进入该项目

```go
// 在 GOPATH 目录之外创建测试项目
mkdir test
// 切换到项目内
cd test
```
创建 `hello.go` 文件。

```
// hello.go
package main

import "bitbucket.org/bigwhite/c"

func main() {
    c.CallC()
}
```

我们构建一下 `hello.go` 这个源码文件：

```go
# go build hello.go
hello.go:3:8: cannot find package "bitbucket.org/bigwhite/c" in any of:
    /root/.bin/go-tip/src/bitbucket.org/bigwhite/c (from $GOROOT)
    /root/go/src/bitbucket.org/bigwhite/c (from $GOPATH)
```

​	构建错误！错误原因很明了：在本地的GOPATH下并没有找到bitbucket.org/bigwhite/c路径的package c。传统fix这个问题的方法是手工将package c通过go get下载到本地(并且go get会自动下载package c所依赖的package d)：

```go
# go get bitbucket.org/bigwhite/c
# go run hello.go
call C: master branch
   --> call D:
    call D: master branch
   --> call D end
```



​	这种我们最熟悉的Go compiler从$GOPATH下(以及vendor目录下)搜索目标程序的依赖包的模式称为：**“GOPATH mode”**。

​	使用 `Go module`（也称之为`module-aware mode`）解决该问题：

​	我们首先在项目目录（./test）下创建下创建go.mod:

```go
// go.mod
module hello
```

然后构建 `hello.go`

```go
# go build hello.go
go: finding bitbucket.org/bigwhite/d v0.0.0-20180714005150-3e3f9af80a02
go: finding bitbucket.org/bigwhite/c v0.0.0-20180714063616-861b08fcd24b
go: downloading bitbucket.org/bigwhite/c v0.0.0-20180714063616-861b08fcd24b
go: downloading bitbucket.org/bigwhite/d v0.0.0-20180714005150-3e3f9af80a02

# ./hello
call C: master branch
   --> call D:
    call D: master branch
   --> call D end
```

在“module-aware mode”下，go compiler将下载的依赖包缓存在$GOPATH/src/xxxx下面：

```go
// $GOPATH/src/xxx     xxx 为包名
# tree -L 3
.
├── bitbucket.org
│   └── bigwhite
│       ├── c@v0.0.0-20180714063616-861b08fcd24b
│       └── d@v0.0.0-20180714005150-3e3f9af80a02
├── cache
│   ├── download
│   │   ├── bitbucket.org
│   │   ├── golang.org
│   │   └── rsc.io
│   └── vcs
│       ├── 064503657de46d4574a6ab937a7a3b88fee03aec15729f7493a3dc8e35cc6d80
│       ├── 064503657de46d4574a6ab937a7a3b88fee03aec15729f7493a3dc8e35cc6d80.info
│       ├── 0c8659d2f971b567bc9bd6644073413a1534735b75ea8a6f1d4ee4121f78fa5b
... ...
```

参考资料：

- [初探 Go module](https://studygolang.com/articles/13697?fr=sidebar)
- [Go Modules 使用教程](https://studygolang.com/articles/15531)
- [Go module 在回顾](https://studygolang.com/articles/23651?fr=sidebar)
- [go: modules disabled inside](http://adophper.com/article-id-1122.html)