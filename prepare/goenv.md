[TOC]

# Go环境变量

为方便开发，在开发环境往往需要设置三个环境变量：

1. `$GOROOT`：go的安装目录，配置后不会再更改；
1. `$GOPATH`：go项目在本地的开发环境的的项目根路径(以便项目编译，`go build`, `go install`)，不同的项目在编译的时候该环境变量可以不同；
1. `$PATH`（重要）：需要将go的bin目录添加到系统`$PATH`中以便方便使用go的相关命令，配置后也不会再更改；

Go的环境变量在官方文档中也有详情的说明，请参考链接：[https://golang.google.cn/doc/install/source](https://golang.google.cn/doc/install/source)

> 环境变量中的`$GOOS`和`$GOARCH`是比较实用的两个变量，可以用在不同平台的交叉编译中，只需要在`go build`之前设置这两个变量即可，这也是go语言的优势之一：可以编译生成跨平台运行的可执行文件。感觉比QT更高效更轻量级，虽然生成的可执行文件是大了一点，不过也在可接受的范围之内。
例如，在`Linux amd64`架构下编译`Windows x86`的可执行文件，可以使用如下命令：
```
CGO_ENABLED=0 GOOS=windows GOARCH=386 go build hello.go
```
遗憾的是交叉编译暂不支持`cgo`方式，因此需要将环境变量`$CGO_ENABLED`设置为0，这样执行之后会在当前目录生成一个`hello.exe`的`windows x86`架构的可执行文件。

# 环境变量设置

除了`$PATH`环境外，其他环境变量都是可选的。

为什么说这个步骤可选呢？因为未来的Go版本慢慢开始移除对`$GOPATH`/`$GOROOT`的支持。此外，在Goland这个IDE中集成有`Terminal`功能，直接使用这个功能中已经设置好了环境变量。

![](/images/goland7.png)


## `*nix`下设置环境变量
在`*nix`系统下(`Linux/Unix/MacOS/*BSD`等等)，需要在`/etc/profile`中增加以下环境变量设置后，执行命令`#source /etc/profile`重新加载profile配置文件（或重新登录），将以下变量添加到用户的环境变量中:
```shell
export GOROOT=/usr/local/go
export GOPATH=/Users/john/Workspace/Go/GOPATH
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

## `Windows`下设置环境变量
Windows如何修改系统环境变量，以及修改环境变量`PATH`，请参考网上教程([百度](https://www.baidu.com/s?wd=Windows%20%E4%BF%AE%E6%94%B9%E7%B3%BB%E7%BB%9F%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%20PATH) 或 [Google](https://www.google.com/search?q=Windows+修改系统环境变量+PATH))。