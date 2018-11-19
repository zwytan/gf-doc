
[TOC]

# 事件回调注册

![](/images/QQ图片20180417140149.png)

`ghttp.Server`提供了事件回调注册功能，类似于其他框架的`中间件`功能，相比较于`中间件`，事件回调的特性更加简单。`ghttp.Server`支持用户对于某一事件进行自定义监听处理，按照```pattern```方式进行绑定注册(```pattern```格式与服务注册一致)。**支持多个方法对同一事件进行监听**，```ghttp.Server```将会按照`路由优先级`及`回调注册顺序`进行回调方法调用。同一事件时先注册的HOOK回调函数优先级越高。
相关方法如下：
```go
func (s *Server) BindHookHandler(pattern string, hook string, handler HandlerFunc) error
func (s *Server) BindHookHandlerByMap(pattern string, hookmap map[string]HandlerFunc) error
```
当然域名对象也支持事件回调注册：
```go
func (d *Domain) BindHookHandler(pattern string, hook string, handler HandlerFunc) error
func (d *Domain) BindHookHandlerByMap(pattern string, hookmap map[string]HandlerFunc) error
```

支持的Hook事件列表：
1. `BeforeServe/ghttp.HOOK_BEFORE_SERVE`

	在进入/初始化服务对象之前。

1. `AfterServe/ghttp.HOOK_AFTER_SERVE`

	在完成服务执行流程之后。

1. `BeforeOutput/ghttp.HOOK_BEFORE_OUTPUT`

	向客户端输出返回内容之前。

1. `AfterOutput/ghttp.HOOK_AFTER_OUTPUT`

	向客户端输出返回内容之后。

1. `BeforeClose/ghttp.HOOK_BEFORE_CLOSE`

	在http请求关闭之前（注意请求关闭是异步处理操作，没有在http执行流程中处理）。

1. `AfterClose/ghttp.HOOK_AFTER_CLOSE`

	在http请求关闭之后（注意请求关闭是异步处理操作，没有在http执行流程中处理）。

具体调用时机请参考图例所示。

## 事件优先级

由于事件的绑定也是使用的路由规则，因此它的优先级和【[路由控制](net/ghttp/router.md)】章节介绍的优先级是一样的。

但是事件调用时和服务注册调用时的机制不一样，**同一个路由规则下允许绑定多个事件回调方法**，该路由下的事件调用会`按照优先级进行调用`，假如优先级相等的路由规则，将会按照事件注册的顺序进行调用。

### 关于全局回调

我们往往使用绑定`/*`这样的HOOK路由来实现全局的回调处理，这样是可以的。但是HOOK执行的优先是最低的，路由注册的越精确，优先级越高，越模糊的路由优先级越低，`/*`就属于最模糊的路由。

为降低不同的模块耦合性，所有的路由往往不是在同一个地方进行注册。例如用户模块注册的HOOK(`/user/*`)，它将会被优先调用随后才可能是全局的HOOK；如果仅仅依靠注册顺序来控制优先级，在模块多路由多的时候优先级便很难管理。

### 关于HOOK中得业务函数调用顺序
建议 相同的业务(同一业务模块) 的多个处理函数(例如: A、B、C)放到同一个HOOK回调函数中进行处理，在注册的回调函数中自行管理业务处理函数的调用顺序(函数调用顺序: A、B、C)，而不是注册多个相同HOOK的回调函数。虽然功能上不会有问题，从设计的角度来讲，内聚性降低了，不便于业务函数管理。

## 示例1，基本使用
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    // 基本事件回调使用
    p := "/:name/info/{uid}"
    s := g.Server()
    s.BindHookHandlerByMap(p, map[string]ghttp.HandlerFunc{
        ghttp.HOOK_BEFORE_SERVE  : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_BEFORE_SERVE) },
        ghttp.HOOK_AFTER_SERVE   : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_AFTER_SERVE) },
        ghttp.HOOK_BEFORE_OUTPUT : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_BEFORE_OUTPUT) },
        ghttp.HOOK_AFTER_OUTPUT  : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_AFTER_OUTPUT) },
        ghttp.HOOK_BEFORE_CLOSE  : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_BEFORE_CLOSE) },
        ghttp.HOOK_AFTER_CLOSE   : func(r *ghttp.Request){ glog.Println(ghttp.HOOK_AFTER_CLOSE) },
    })
    s.BindHandler(p, func(r *ghttp.Request) {
       r.Response.Write("用户:", r.Get("name"), ", uid:", r.Get("uid"))
    })
    s.SetPort(8199)
    s.Run()
}
```
当访问```http://127.0.0.1:8199/john/info/10000```时，运行Web Server进程的终端将会按照事件的执行流程打印出对应的事件名称。

## 示例2，相同事件注册
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 优先调用的HOOK
func beforeServeHook1(r *ghttp.Request) {
    r.SetParam("name", "GoFrame")
    r.Response.Writeln("set name")
}

// 随后调用的HOOK
func beforeServeHook2(r *ghttp.Request) {
    r.SetParam("site", "https://gfer.me")
    r.Response.Writeln("set site")
}

// 允许对同一个路由同一个事件注册多个回调函数，按照注册顺序进行优先级调用。
// 为便于在路由表中对比查看优先级，这里讲HOOK回调函数单独定义为了两个函数。
func main() {
    s := g.Server()
    s.BindHandler("/", func(r *ghttp.Request) {
        r.Response.Writeln(r.GetParam("name").String())
        r.Response.Writeln(r.GetParam("site").String())
    })
    s.BindHookHandler("/", ghttp.HOOK_BEFORE_SERVE, beforeServeHook1)
    s.BindHookHandler("/", ghttp.HOOK_BEFORE_SERVE, beforeServeHook2)
    s.SetPort(8199)
    s.Run()
}
```
执行后，终端输出的路由表信息如下：
```
  SERVER  | ADDRESS | DOMAIN  | METHOD | P | ROUTE |        HANDLER        |    HOOK      
|---------|---------|---------|--------|---|-------|-----------------------|-------------|
  default | :8199   | default | ALL    | 1 | /     | main.main.func1       |              
|---------|---------|---------|--------|---|-------|-----------------------|-------------|
  default | :8199   | default | ALL    | 2 | /     | main.beforeServeHook1 | BeforeServe  
|---------|---------|---------|--------|---|-------|-----------------------|-------------|
  default | :8199   | default | ALL    | 1 | /     | main.beforeServeHook2 | BeforeServe  
|---------|---------|---------|--------|---|-------|-----------------------|-------------|
```
手动访问 [http://127.0.0.1:8199/](http://127.0.0.1:8199/) 后，页面输出内容为：
```
set name
set site
GoFrame
https://gfer.me
```

## 示例3，改变业务逻辑
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := g.Server()

    // 多事件回调示例，事件1
    pattern1 := "/:name/info"
    s.BindHookHandlerByMap(pattern1, map[string]ghttp.HandlerFunc {
        "BeforeServe"  : func(r *ghttp.Request) {
            r.SetQuery("uid", "1000")
        },
    })
    s.BindHandler(pattern1, func(r *ghttp.Request) {
        r.Response.Write("用户:", r.Get("name"), ", uid:", r.GetQueryString("uid"))
    })

    // 多事件回调示例，事件2
    pattern2 := "/{object}/list/{page}.java"
    s.BindHookHandlerByMap(pattern2, map[string]ghttp.HandlerFunc {
        "BeforeOutput" : func(r *ghttp.Request){
            r.Response.SetBuffer([]byte(
                fmt.Sprintf("通过事件修改输出内容, object:%s, page:%s", r.Get("object"), r.GetRouterString("page"))),
            )
        },
    })
    s.BindHandler(pattern2, func(r *ghttp.Request) {
        r.Response.Write(r.Router.Uri)
    })
    s.SetPort(8199)
    s.Run()
}
```

通过事件1设置了访问```/:name/info```路由规则时的GET参数；通过事件2，改变了当访问的路径匹配路由```/{object}/list/{page}.java```时的输出结果。

## 示例4，事件回调注册优先级
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/os/glog"
)

func main() {
    s := g.Server()
    s.BindHandler("/priority/show", func(r *ghttp.Request) {
        r.Response.Write("priority test")
    })

    s.BindHookHandlerByMap("/priority/:name", map[string]ghttp.HandlerFunc {
        "BeforeServe"  : func(r *ghttp.Request) {
            glog.Println(r.Router.Uri)
        },
    })
    s.BindHookHandlerByMap("/priority/*any", map[string]ghttp.HandlerFunc {
        "BeforeServe"  : func(r *ghttp.Request) {
            glog.Println(r.Router.Uri)
        },
    })
    s.BindHookHandlerByMap("/priority/show", map[string]ghttp.HandlerFunc {
        "BeforeServe"  : func(r *ghttp.Request) {
            glog.Println(r.Router.Uri)
        },
    })
    s.SetPort(8199)
    s.Run()
}
```
在这个示例中，我们往注册了3个路由规则的事件回调，并且都匹配服务注册的地址```/priority/show```，这样我们便可以通过访问这个地址来看看路由执行的顺序是怎么样的。

执行后我们访问```http://127.0.0.1:8199/priority/show```，随后我们可以在服务端的终端上看到以下输出信息：
```html
2018-08-03 15:16:25.971 27391: http server started listening on [:8199]
2018-08-03 15:16:28.385 /priority/show
2018-08-03 15:16:28.385 /priority/:name
2018-08-03 15:16:28.385 /priority/*any
```

## 示例5，使用事件回调允许跨域请求
首先我们来看一个简单的`REST`接口示例：
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/frame/gmvc"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Order struct {
    gmvc.Controller
}

func (o *Order) Get() {
    o.Response.Write("GET")
}

func main() {
    s := g.Server()
    s.BindControllerRest("/api.v1/{.struct}", new(Order))
    s.SetPort(8199)
    s.Run()
}
```
接口地址是```http://localhost/api.v1/order```，当然这个接口是不允许跨域的。我们打开一个不同的域名，例如：百度首页(正好用了jQuery，方便调试)，然后按```F12```打开开发者面板，在```console```下执行以下AJAX请求：
```javascript
$.get("http://localhost:8199/api.v1/order", function(result){
    console.log(result)
});
```
结果如下：
![](/images/Selection_154.png)
返回了不允许跨域的错误，接着我们修改一下测试代码，如下：

```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/frame/gmvc"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Order struct {
    gmvc.Controller
}

func (o *Order) Get() {
    o.Response.Write("GET")
}

func main() {
    s := g.Server()
    s.BindHookHandlerByMap("/api.v1/*any", map[string]ghttp.HandlerFunc {
       "BeforeServe"  : func(r *ghttp.Request) {
           r.Response.SetAllowCrossDomainRequest("*", "PUT,GET,POST,DELETE,OPTIONS")
       },
    })
    s.BindControllerRest("/api.v1/{.struct}", new(Order))
    s.SetPort(8199)
    s.Run()
}
```
我们增加了针对于路由```/api.v1/*any```的绑定事件```BeforeServe```，该事件将会在所有服务执行之前调用，该事件的回调方法中，我们通过调用```SetAllowCrossDomainRequest```方法设置运行跨域请求。该绑定的事件路由规则使用了模糊匹配规则，表示所有```/api.v1```开头的接口地址都允许跨域请求。

返回刚才的百度首页，再次执行请求AJAX请求，这次便成功了：
![](/images/Selection_155.png)
