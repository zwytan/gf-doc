
[TOC]

ghttp提供了完善的Web Server日志管理功能，包括```access log```以及```error log```，并可自定义日志输出回调方法，开发者可灵活使用。

>[danger] # 请求日志

我们来看一个简单的例子：
```go
package main

import (
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/log/access", func(r *ghttp.Request){
        r.Response.Writeln("请在运行终端查看日志输出")
    })
    s.SetAccessLogEnabled(true)
    s.SetPort(8199)
    s.Run()
}
```

我们在任何地方(包括运行时)可以调用```SetAccessLogEnabled(true)```开启access log的记录功能(并可通过```SetAccessLogEnabled(false)```随时动态关闭日志记录功能)，默认情况下，日志内容将会输出到终端界面。如以上示例程序执行后，访问```http://127.0.0.1:8199/log/access```，日志内容将会输出到终端上，如下：
```shell
2018-04-20 18:11:57.344 "GET 127.0.0.1:8199 /log/access HTTP/1.1" 200 16 0.120, 127.0.0.1, "", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/53.0.2785.143 Chrome/53.0.2785.143 Safari/537.36"
```
ghttp的access log将会记录访问时间、请求方式、请求地址、请求协议、处理状态码、返回字节大小、执行时间、来源客户端IP、来源URL地址、UserAgent信息。

>[danger] # 错误日志

Web Server运行产生的任何错误信息，都将会被记录到error log中，以下是一个手动触发异常的示例：

```go
package main

import (
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/log/error", func(r *ghttp.Request){
        panic("异常信息")
    })
    s.SetErrorLogEnabled(true)
    s.SetPort(8199)
    s.Run()
}
```

运行并访问后，终端输出的错误日志信息如下：
```shell
2018-04-20 18:31:03.484 [ERRO] "GET 127.0.0.1:8199 /log/error HTTP/1.1" 0.098, 127.0.0.1, "", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/53.0.2785.143 Chrome/53.0.2785.143 Safari/537.36", 异常信息
Trace:
1. /home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/geg/net/ghttp/log.go:10
2. /home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/net/ghttp/http_server_handler.go:83
3. /home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/net/ghttp/http_server_handler.go:52
4. /home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/net/ghttp/http_server_handler.go:25
5. /home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/net/ghttp/http_server.go:137
```
错误信息会打印出对应错误产生的```caller backtrace```，以便于错误定位以及开发者分析问题原因。


>[danger] # 输出日志到文件

我们可以通过```SetLogPath```方法来设置日志目录，这样我们的日志信息将会保存到文件中，示例如下：

```go
package main

import (
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/log/path", func(r *ghttp.Request){
        r.Response.Writeln("请到/tmp/gf.log目录查看日志")
    })
    s.SetLogPath("/tmp/gf.log")
    s.SetAccessLogEnabled(true)
    s.SetErrorLogEnabled(true)
    s.SetPort(8199)
    s.Run()
}
```
运行并访问后，日志将会被保存到```/tmp/gf.log/access```目录中，其中```access```目录为请求日志，此外错误日志将会写入到```error```目录中。手动在终端查看目录结构如下：
```shell
john@johnstation:/tmp$ tree gf.log
gf.log/
├── access
│   └── 2018-04-20.log
└── error
    └── 2018-04-20.log
```




>[danger] # 自定义日志处理

ghttp.Server支持自定义的日志处理特性，便于开发者灵活控制日志输出，使用```SetLogHandler```来实现日志回调方法注册。

以下是自定义日志处理的简单示例：
```go
package main

import (
    "fmt"
    "net/http"
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/log/handler", func(r *ghttp.Request){
        r.Response.WriteStatus(http.StatusNotFound, "文件找不到了")
    })
    s.SetAccessLogEnabled(true)
    s.SetErrorLogEnabled(true)
    s.SetLogHandler(func(r *ghttp.Request, error ...interface{}) {
        if len(error) > 0 {
            // 如果是错误日志
            fmt.Println("错误产生了：", error[0])
        }
        // 这里是请求日志
        fmt.Println("请求处理完成，请求地址:", r.URL.String(), "请求结果:", r.Response.Status)
    })
    s.SetPort(8199)
    s.Run()
}
```

运行并请求后，终端输出内容如下：
```shell
请求处理完成，请求地址: /log/handler 请求结果: 404
```