我们先来看看Cookie对象有哪些方法：

    func (c *Cookie) Close()
    func (c *Cookie) Get(key string) string
    func (c *Cookie) Output()
    func (c *Cookie) Remove(key, domain, path string)
    func (c *Cookie) SessionId() string
    func (c *Cookie) Set(key, value string)
    func (c *Cookie) SetCookie(key, value, domain, path string, maxage int)
    func (c *Cookie) SetSessionId(sessionid string)
    
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

任何时候都可以通过 *ghttp.ClientRequest 获取Cookie对象，因为Cookie和Session都是和请求会话相关，因此都属于ClientRequest的成员变量，并对外公开。
