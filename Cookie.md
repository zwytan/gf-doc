
[TOC]


>[danger] # 方法列表

我们先来看看Cookie操作有哪些相关方法：

```go
func (c *Cookie) Close()
func (c *Cookie) Get(key string) string
func (c *Cookie) Output()
func (c *Cookie) Remove(key, domain, path string)
func (c *Cookie) SessionId() string
func (c *Cookie) Set(key, value string)
func (c *Cookie) SetCookie(key, value, domain, path string, maxage int)
func (c *Cookie) SetSessionId(sessionid string)

func (s *Server) GetCookieMaxAge() int
func (s *Server) SetCookieMaxAge(maxage int)
```
任何时候都可以通过 \*ghttp.Request 对象获取到当前请求对应的Cookie对象，因为Cookie和Session都是和请求会话相关，因此都属于ghttp.Request的成员对象，并对外公开。

此外，Cookie中封装了两个SessionId相关的方法，Cookie.SessionId()用于获取当前请求的SessionId，每个请求的SessionId都是唯一的，并且伴随整个请求流程。Cookie.SetSessionId(session string)用于自定义设置当前的SessionId，可以实现自定义的Session控制，以及Session跨域支持(特别是多服务器集群环境下)。

在任何时候，我们都可以通过ghttp.Server对象来修改和获取Cookie的过期时间。

>[danger] # 使用示例

gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/cookie.go

```go
package demo

import (
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/net/ghttp"
)

func init() {
    ghttp.GetServer().BindHandler("/cookie", Cookie)
}

func Cookie(r *ghttp.Request) {
    datetime := r.Cookie.Get("datetime")
    r.Cookie.Set("datetime", gtime.Datetime())
    r.Response.WriteString("datetime:" + datetime)
}
```
执行外层的main.go，可以尝试刷新页面 http://127.0.0.1:8199/cookie ，显示的时间在一直变化。


对于控制器对象而言，从基类控制器中继承了很多会话相关的对象指针，可以看做alias，可以直接使用，他们都是指向的同一个对象：
```go
type Controller struct {
    Request  *ghttp.Request  // 请求数据对象
    Response *ghttp.Response // 返回数据对象(r.Response)
    Server   *ghttp.Server   // Web Server对象(r.Server)
    Cookie   *ghttp.Cookie   // COOKIE操作对象(r.Cookie)
    Session  *ghttp.Session  // SESSION操作对象
    View     *View           // 视图对象
}
```

由于对于Web开发者来讲，Cookie都已经是非常熟悉的组件了，相关API也非常简单，这里便不再赘述。