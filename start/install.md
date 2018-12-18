[TOC]

> 该章节仅针对Golang新手，老司机请忽略。

# Go开发环境安装


## Step1 - 下载Go安装文件

访问Go国内镜像站下载页面 [https://golang.google.cn/dl/](https://golang.google.cn/dl/)，并在页面最上方的版本中选择你当前的系统版本，会下载最新版本的Go:
![](/images/downloadgo.png)

## Step2 - 安装引导

访问官方安装介绍页面 [https://golang.google.cn/doc/install](https://golang.google.cn/doc/install)，按照当前系统版本执行对应的安装流程即可。

Windows(`msi`)和MacOS(`pkg`)推荐使用安装包的方式来安装。作者当前MacOS安装包(`pkg`)安装如下图所示：
![](/images/goinstall-macos-1.png)

一直下一步即可。

![](/images/goinstall-macos-2.png)

![](/images/goinstall-macos-3.png)


# IDE开发环境安装

目前Go的IDE有两款比较流行，一款是国人开发的`LiteIDE`（免费），另一款是Jetbrains公司的`Goland`（收费），我们推荐使用`Goland`来作为开发IDE，下载及注册请参考网上教程（[百度](https://www.baidu.com/s?wd=goland%20安装) 或 [Google](https://www.google.com/search?q=goland+安装)）。

## Goland的使用

### Step1 - 打开IDE
![](/images/goland0.png)


### Step2 - 创建项目
这里需要注意的是Go安装文件的路径，官方文档有说明，请仔细阅读。
![](/images/goland2.png)


### Step3 - 创建程序
![](/images/goland3.png)


### Step4 - 执行运行
![](/images/goland4.png)

![](/images/goland5.png)

![](/images/goland6.png)

### Step5 - GOROOT及GOPATH
可选，但是尽量设置一下，以下简单说明，详细操作自行研究。

一般来说我们还需要在环境变量中设置`GOROOT`和`GOPATH`，例如在*nix系统下，需要在`/etc/profile`中增加以下环境变量设置:
```shell
# go bin
export GOROOT=/usr/local/go
export GOPATH=/Users/john/Workspace/Go/GOPATH
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

Windows需要修改`PATH`。


### Step6 - Go Modules包管理

推荐启用和使用Go官方的`Go Modules`包管理特性，这块具体的设置也留给开发者自行研究。

