我们先来看看Cookie对象有哪些方法：

    func (c *Cookie) Close()
    func (c *Cookie) Get(key string) string
    func (c *Cookie) Output()
    func (c *Cookie) Remove(key, domain, path string)
    func (c *Cookie) SessionId() string
    func (c *Cookie) Set(key, value string)
    func (c *Cookie) SetCookie(key, value, domain, path string, maxage int)
    func (c *Cookie) SetSessionId(sessionid string)
    
任何时候都可以通过 *ghttp.ClientRequest 获取Cookie对象，因为Cookie和Session都是和请求会话相关，因此都属于ClientRequest的成员变量，并对外公开。

示例(gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/cookie.go)：
```go
package demo

import (
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/net/ghttp"
)

func init() {
    ghttp.GetServer().BindHandler("/cookie", Cookie)
}

func Cookie(s *ghttp.Server, r *ghttp.ClientRequest, w *ghttp.ServerResponse) {
    datetime := r.Cookie.Get("datetime")
    r.Cookie.Set("datetime", gtime.Datetime())
    w.WriteString("datetime:" + datetime)
}
```

对于控制器对象而言，从基类控制器中集成了很多会话相关的对象队长，也可以直接使用，他们都是指向的同一个对象：
```go
// 控制器基类
type Controller struct {
    Server   *ghttp.Server         // Web Server对象
    Request  *ghttp.ClientRequest  // 请求数据对象
    Response *ghttp.ServerResponse // 返回数据对象
    Cookie   *ghttp.Cookie         // COOKIE操作对象
    Session  *ghttp.Session        // SESSION操作对象
    View     *View                 // 视图对象
}
```

这个基类控制器我复制-粘贴两次了，后面我就不再粘贴了哈 ^_^。