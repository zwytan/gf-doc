***gf框架的Cookie、Session及模板引擎变量是并发安全的，不支持以map的形式直接读取/操作数据***

我们先来看看Cookie对象有哪些方法：

    func (c *Cookie) Close()
    func (c *Cookie) Get(key string) string
    func (c *Cookie) Output()
    func (c *Cookie) Remove(key, domain, path string)
    func (c *Cookie) SessionId() string
    func (c *Cookie) Set(key, value string)
    func (c *Cookie) SetCookie(key, value, domain, path string, maxage int)
    func (c *Cookie) SetSessionId(sessionid string)
    
任何时候都可以通过 *ghttp.Request 获取Cookie对象，因为Cookie和Session都是和请求会话相关，因此都属于ghttp.Request的成员对象，并对外公开。

来一个例子：
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


对于控制器对象而言，从基类控制器中继承了很多会话相关的对象指针，也可以直接使用，他们都是指向的同一个对象：
```go
type Controller struct {
    Request  *ghttp.Request  // 请求数据对象
    Response *ghttp.Response // 返回数据对象(r.Response)
    Server   *ghttp.Server   // Web Server对象(r.Server)
    Cookie   *ghttp.Cookie   // COOKIE操作对象
    Session  *ghttp.Session  // SESSION操作对象
    View     *View           // 视图对象
}
```

这个基类控制器我复制-粘贴两次了，后面我就不再粘贴了哦 ^_^。