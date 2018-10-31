# FAQ/常见问题

## 1. 在`init`包初始化方法中执行服务注册，但是却访问不到注册的路由
我按照文档上面的示例进行执行对象注册，但是运行后访问注册的路由（`http://127.0.0.1/object`）提醒我`404`，好着急啊，我在线等。
```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func init() {
    g.Server().BindObject("/object", new(Object))
}

func (o *Object) Index(r *ghttp.Request) {
    r.Response.Write("object index")
}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Write("object show")
}
```

回答：

1. 如何判断路由是否执行成功？在程序运行后终端会打印出当前注册的路由表信息，检查是否注册成功。
1. 该问题是没有在`main`包中引入`demo`包造成的，引入方式:
    ```go
    import _ "PATH/TO/YOUR/PROJECT/PACKAGE"
    ```
    文档链接: 【[服务注册-基本介绍](net/ghttp/service/index。md)】













