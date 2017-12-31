与Session相关的方法：

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

任何时候都可以通过 *ghttp.ClientRequest 获取Session对象，因为Cookie和Session都是和请求会话相关，因此都属于ClientRequest的成员变量，并对外公开。

示例(gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/session.go)：
```go
package demo

import (
    "strconv"
    "gitee.com/johng/gf/g/net/ghttp"
)

func init() {
    ghttp.GetServer().BindHandler("/session", Session)
}

// 用于函数映射
func Session(s *ghttp.Server, r *ghttp.ClientRequest, w *ghttp.ServerResponse) {
    id := r.Session.GetInt("id")
    r.Session.Set("id", id + 1)
    w.WriteString("id:" + strconv.Itoa(id))
}
```