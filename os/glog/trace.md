# Trace特性

错误日志信息支持`trace`特性，可以通过`Notice*/Warning*/Error*/Critical*/Panic*/Fatal*`错误方法触发，也可以通过`GetBacktrace/PrintBacktrace`获取/打印。错误信息的`trace`信息对于调试错误来说相当有用。

## 示例1，通过错误方法触发
```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
)

func main() {
    glog.Error("发生错误！")
}
```
打印出的结果如下：
```html
2018-01-11 17:20:10 [ERRO] 发生错误！
Trace:
1. /home/john/Workspace/Go/github.com/gogf/gf/geg/other/test.go:8
```

## 示例2，通过trace方法打印

```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
    "fmt"
)

func main() {

    glog.PrintBacktrace()
    glog.New().PrintBacktrace()

    fmt.Println(glog.GetBacktrace())
    fmt.Println(glog.New().GetBacktrace())
}
```
执行后，输出结果为：
```html
2018-10-10 15:18:34.346 1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:10

2018-10-10 15:18:34.346 1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:11

1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:13

1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:14
```