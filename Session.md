
[TOC]


>[danger] # 方法列表

与Session操作相关的方法：
```go
func (s *Session) BatchSet(m map[string]interface{})
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
任何时候都可以通过```*ghttp.Request```获取Session对象，因为Cookie和Session都是和请求会话相关，因此都属于Request的成员对象，并对外公开。gf框架的Session是存放在内存中的，因此处理效率非常高，默认过期时间是600秒。

此外，需要说明的是，Session的操作是支持并发安全的，这也是框架在对Session的设计上不采用直接以map的形式操作数据的原因。

在任何时候，我们都可以通过ghttp.Server对象来修改和获取Session的全局相关属性。

>[danger] # 使用示例

gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/session.go

```go
package demo

import (
    "strconv"
    "gitee.com/johng/gf/g/net/ghttp"
)

func init() {
    ghttp.GetServer().BindHandler("/session", Session)
}

func Session(r *ghttp.Request) {
    id := r.Session.GetInt("id")
    r.Session.Set("id", id + 1)
    r.Response.Write("id:" + strconv.Itoa(id))
}
```
启动main.go，访问 http://127.0.0.1:8199/session ，刷新几次页面，可以看到页面输出的id值在不断递增。


由于对于Web开发者来讲，Session都已经是非常熟悉的组件了，相关API也非常简单，这里便不再赘述。