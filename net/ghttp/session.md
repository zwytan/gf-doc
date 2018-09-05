
[TOC]


# 方法列表

与`Session`操作相关的方法：
[godoc.org/github.com/johng-cn/gf/g/net/ghttp#Session](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp#Session)
```go
func (s *Session) BatchSet(m map[string]interface{})
func (s *Session) Contains(k string) bool
func (s *Session) Data() map[string]interface{}
func (s *Session) Get(k string) interface{}
func (s *Session) GetBool(k string) bool
func (s *Session) GetFloat32(k string) float32
func (s *Session) GetFloat64(k string) float64
func (s *Session) GetInt(k string) int
func (s *Session) GetString(k string) string
func (s *Session) GetUint(k string) uint
func (s *Session) Id() string
func (s *Session) Remove(k string)
func (s *Session) Set(k string, v interface{})
func (s *Session) UpdateExpire()

func (s *Server) GetSessionIdName() string
func (s *Server) GetSessionMaxAge() int
func (s *Server) SetSessionIdName(name string)
func (s *Server) SetSessionMaxAge(maxage int)
```
任何时候都可以通过```*ghttp.Request```获取`Session`对象，因为`Cookie`和`Session`都是和请求会话相关，因此都属于`Request`的成员对象，并对外公开。gf框架的`Session`是存放在内存中的，因此处理效率非常高，默认过期时间是`600秒`。

此外，需要说明的是，`Session`的操作是支持`并发安全`的，这也是框架在对`Session`的设计上不采用直接以map的形式操作数据的原因。

在任何时候，我们都可以通过`ghttp.Request`对象来修改和获取`Session`的全局相关属性。

# 使用示例

```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindHandler("/session", func(r *ghttp.Request) {
        id := r.Session.GetInt("id")
        r.Session.Set("id", id + 1)
        r.Response.Write("id:", id)
    })
    s.SetPort(8199)
    s.Run()
}
```
启动`main.go`，访问```http://127.0.0.1:8199/session```，刷新几次页面，可以看到页面输出的id值在不断递增。


由于对于Web开发者来讲，`Session`都已经是非常熟悉的组件了，相关API也非常简单，这里便不再赘述。