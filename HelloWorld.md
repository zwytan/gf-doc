老规矩，我们先来一个Hello World：
```go
package demo

import "gitee.com/johng/gf/g/net/ghttp"

func init() {
    ghttp.GetServer().BindHandler("/", func(r *ghttp.Request){
        r.Response.WriteString("Hello World!")
    })
}
```
这便是一个最简单的Web Server，它不支持静态文件处理，只有一个功能，访问 http://127.0.0.1/ 的时候，它会返回“Hello World!”。

任何时候，您都可以通过 ghttp.GetServer()方法获得一个默认的Web Server对象，该方法采用单例模式设计，也就是说，多次调用该方法，返回的是同一个Web Server对象。

通过Run()方法执行Web Server的监听运行，在没有任何额外设置的情况下，它默认监听80端口。

关于其中的服务注册，我们将会在后续章节中介绍，先不着急，我们继续来看看怎么创建一个支持静态文件的Web Server。