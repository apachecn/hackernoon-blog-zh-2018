# 从 dep 迁移到 Go 1.11 模块

> 原文：<https://medium.com/hackernoon/migrating-from-dep-to-go-1-11-modules-bba03c513799>

![](img/f02864bde507936f7633b33f51653291.png)

# TL；速度三角形定位法(dead reckoning)

*   将您的代码移到`GOPATH`之外
*   `go mod init [module path]`:这将从`Gopkg.lock`导入依赖关系。
*   `go mod tidy`:这将删除不必要的导入，并添加间接导入。
*   `rm -fr vendor/`
*   一切都好吗？
*   `rm -f Gopkg.lock Gopkg.toml`
*   `git commit -m 'chore(dep): migrated from dep to Go 1.11 modules'`

# 介绍

在 Go 1.11 之前，依赖[管理](https://hackernoon.com/tagged/management)被留给了[社区](https://hackernoon.com/tagged/community)。有很多解决方案，但我最喜欢的是[](https://golang.github.io/dep/)**。**

**像其他语言的许多依赖项管理工具一样， **dep** 有一个用于依赖项请求的文件(`Gopkg.toml`)、一个锁定所使用的确切版本的文件(`Gopkg.lock`)和一个保存依赖项文件的目录`vendor`。一个简单的命令`[dep ensure](https://golang.github.io/dep/docs/daily-dep.html)`就可以完成所有的工作。**

**此外，在 Go 1.11 之前，您的项目源代码需要在您的`GOPATH`中，并且您必须遵守[工作空间布局](https://golang.org/doc/code.html)。**

**幸运的是，有了 Go 1.11，你的代码可以存在于你磁盘上的任何地方！另外，依赖关系管理由`go`命令处理，引入了[模块](https://github.com/golang/go/wiki/Modules)。**

# **移民**

**安装 Go 1.11 后，从将代码移出`GOPATH`开始:**

**(我的`GOPATH`是`~/code/go/`)**

```
~/code $ mv go/src/gitlab.callr.tech/platform/asterisk-pbx-agi .
```

**现在，我的项目在`~/code/asterisk-pbx-agi`。**

**让我们试试`go mod init`:**

```
~/code/asterisk-pbx-agi $ go mod init 
go: cannot determine module path for source directory ~/code/asterisk-pbx-agi (outside GOPATH, no import comments)
```

**啊。所以因为代码在`GOPATH`之外，go 不能再确定“模块路径”了。有道理。**

**让我们用模块路径再试一次:**

```
~/code/asterisk-pbx-agi $ go mod init gitlab.callr.tech/platform/asterisk-pbx-agi
go: creating new go.mod: module gitlab.callr.tech/platform/asterisk-pbx-agi
go: copying requirements from Gopkg.lock
```

**Go 通过读取`Gopkg.lock`文件从 **dep** 导入了我的依赖项。干净利落。它还创建了一个`go.mod`文件:**

```
module gitlab.callr.tech/platform/asterisk-pbx-agi

require (
    github.com/zaf/agi v0.0.0-20160319110841-15f1ed9d87e3
    go.uber.org/atomic v1.3.2
    go.uber.org/multierr v1.1.0
    go.uber.org/zap v1.8.0
    gopkg.in/yaml.v2 v2.2.1
)
```

**此时，`go build`应该起作用了。**

**不过还是先试一个`go mod tidy`吧。下面是运行后的`go.mod`:**

```
module gitlab.callr.tech/platform/asterisk-pbx-agi

require (
    github.com/davecgh/go-spew v1.1.1 // indirect
    github.com/pkg/errors v0.8.0 // indirect
    github.com/pmezard/go-difflib v1.0.0 // indirect
    github.com/stretchr/testify v1.2.2 // indirect
    github.com/zaf/agi v0.0.0-20160319110841-15f1ed9d87e3
    go.uber.org/atomic v1.3.2 // indirect
    go.uber.org/multierr v1.1.0 // indirect
    go.uber.org/zap v1.8.0
    gopkg.in/yaml.v2 v2.2.1
)
```

**有意思。`go mod tidy`检测到某些依赖关系是“间接的”,并将其标记为间接的。它还增加了一些其他的，需要用于`go test`。**

**当`go build`和`go test`都工作时，您可以安全地删除旧文件:**

```
~/code/asterisk-pbx-agi $ rm -fr Gopkg.* vendor/
```

**你就完了。**

# **更新您的配置项**

**使用 Gitlab，下面是我们过去如何使用 Go 1.10 和 dep 进行构建:**

```
Build Go:
  image: golang:1.10
  stage: build
  script:
    - curl -fsSL -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/v0.4.1/dep-linux-amd64 && chmod +x /usr/local/bin/dep
    - ln -s `pwd` /go/src/asterisk-pbx-agi
    - cd /go/src/asterisk-pbx-agi
    - dep ensure -vendor-only
    - GOOS=linux GOARCH=amd64 go build -ldflags "-linkmode external -extldflags -static" -a -o callr.agi
  artifacts:
    paths:
      - callr.agi
    expire_in: 1 week
```

**由于`GOPATH`的混乱，我们不得不创建一个到`/go/src`内部代码的链接，并在其中运行`dep`和`go build`。**

# **在...之后**

**以下是 Go 1.11 的相同任务:**

```
Build Go:
  image: golang:1.11
  stage: build
  script:
    - GOOS=linux GOARCH=amd64 go build -ldflags "-linkmode external -extldflags -static" -a -o callr.agi
  artifacts:
    paths:
      - callr.agi
    expire_in: 1 week
```

**简单多了。`go build`将自动处理依赖关系。**

# **证明文件**

*   **[Go wiki 模块](https://github.com/golang/go/wiki/Modules)**
*   **[Go 命令文件](https://golang.org/cmd/go/)**

***原载于 2018 年 9 月 11 日*[*blog . callr . tech*](https://blog.callr.tech/migrating-from-dep-to-go-1.11-modules/)*。***