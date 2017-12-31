### Web Server
创建并运行一个Web Server：
```go
package main
import (
    "gitee.com/johng/gf/g/net/ghttp"
)
func main() {
    s := ghttp.GetServer()
    s.SetPort(8199)
    s.SetIndexFolder(true)
    s.SetServerRoot("/tmp")
    s.Run()
}
```
使用ghttp.GetServer()方法可以创建一个Web Server，该方法采用单例模式设计，也就是说，多次调用该方法，返回的是同一个Web Server对象。

创建了Web Server对象之后，我们可以使用对应的各种Set方法来设置Web Server的属性。
1. SetPort 用来设置Web Server的监听端口；
2. SetIndexFolder 用来设置是否允许列出Web Server主目录的文件列表；
3. SetServerRoot 用来设置Web Server的主目录；

以下是Web Server的公开方法：
    func (s *Server) BindController(pattern string, c Controller) error
    func (s *Server) BindControllerMethod(pattern string, c Controller, method string) error
    func (s *Server) BindControllerRest(pattern string, c Controller) error
    func (s *Server) BindHandler(pattern string, handler HandlerFunc) error
    func (s *Server) BindObject(pattern string, obj interface{}) error
    func (s *Server) BindObjectRest(pattern string, obj interface{}) error
    func (s *Server) Domain(domain string) *Domain
    func (s *Server) GetName() string
    func (s *Server) NotFound(w http.ResponseWriter, r *http.Request)
    func (s *Server) ResponseStatus(w http.ResponseWriter, code int)
    func (s *Server) Run() error
    func (s *Server) SetAddr(addr string) error
    func (s *Server) SetConfig(c ServerConfig) error
    func (s *Server) SetErrorLog(logger *log.Logger) error
    func (s *Server) SetIdleTimeout(t time.Duration) error
    func (s *Server) SetIndexFiles(index []string) error
    func (s *Server) SetIndexFolder(index bool) error
    func (s *Server) SetMaxHeaderBytes(b int) error
    func (s *Server) SetPort(port int) error
    func (s *Server) SetReadTimeout(t time.Duration) error
    func (s *Server) SetServerAgent(agent string) error
    func (s *Server) SetServerRoot(root string) error
    func (s *Server) SetTLSConfig(tls *tls.Config) error
    func (s *Server) SetWriteTimeout(t time.Duration) error
### 多服务支持


### 多域名支持