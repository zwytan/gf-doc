
[TOC]

# 配置管理对象
[godoc.org/github.com/gogf/gf/g/net/ghttp#ServerConfig](https://godoc.org/github.com/gogf/gf/g/net/ghttp#ServerConfig)
```go
// HTTP Server 设置结构体，静态配置
type ServerConfig struct {
    // 底层http对象配置
    Addr             string        // 监听IP和端口，监听本地所有IP使用":端口"(支持多个地址，使用","号分隔)
    HTTPSAddr        string        // HTTPS服务监听地址(支持多个地址，使用","号分隔)
    HTTPSCertPath    string        // HTTPS证书文件路径
    HTTPSKeyPath     string        // HTTPS签名文件路径
    Handler          http.Handler  // 默认的处理函数
    ReadTimeout      time.Duration // 读取超时
    WriteTimeout     time.Duration // 写入超时
    IdleTimeout      time.Duration // 等待超时
    MaxHeaderBytes   int           // 最大的header长度

    // 静态文件配置
    IndexFiles       []string      // 默认访问的文件列表
    IndexFolder      bool          // 如果访问目录是否显示目录列表
    ServerAgent      string        // server agent
    ServerRoot       string        // 服务器服务的本地目录根路径

    // COOKIE
    CookieMaxAge     int          // Cookie有效期
    CookiePath       string       // Cookie有效Path(注意同时也会影响SessionID)
    CookieDomain     string       // Cookie有效Domain(注意同时也会影响SessionID)

    // SESSION
    SessionMaxAge    int          // Session有效期
    SessionIdName    string       // SessionId名称

    // ip访问控制
    DenyIps          []string     // 不允许访问的ip列表，支持ip前缀过滤，如: 10 将不允许10开头的ip访问
    AllowIps         []string     // 仅允许访问的ip列表，支持ip前缀过滤，如: 10 将仅允许10开头的ip访问
    // 路由访问控制
    DenyRoutes       []string     // 不允许访问的路由规则列表

    // 日志配置
    LogPath          string       // 存放日志的目录路径
    LogHandler       LogHandler   // 自定义日志处理回调方法
    ErrorLogEnabled  bool         // 是否开启error log
    AccessLogEnabled bool         // 是否开启access log

    // 其他设置
    NameToUriType    int          // 服务注册时对象和方法名称转换为URI时的规则
    GzipContentTypes []string     // 允许进行gzip压缩的文件类型
    DumpRouteMap     bool         // 是否在程序启动时默认打印路由表信息
}
```


# 配置管理方法
[godoc.org/github.com/gogf/gf/g/net/ghttp#Server](https://godoc.org/github.com/gogf/gf/g/net/ghttp#Server)
```go
func (s *Server) AddSearchPath(path string) error
func (s *Server) DumpRoutesMap()
func (s *Server) EnableAdmin(pattern ...string)
func (s *Server) EnableHTTPS(certFile, keyFile string)
func (s *Server) EnablePprof(pattern ...string)
func (s *Server) GetCookieDomain() string
func (s *Server) GetCookieMaxAge() int
func (s *Server) GetCookiePath() string
func (s *Server) GetLogHandler() LogHandler
func (s *Server) GetLogPath() string
func (s *Server) GetName() string
func (s *Server) GetRouteMap() string
func (s *Server) GetSessionIdName() string
func (s *Server) GetSessionMaxAge() int
func (s *Server) IsAccessLogEnabled() bool
func (s *Server) IsErrorLogEnabled() bool
func (s *Server) SetAccessLogEnabled(enabled bool)
func (s *Server) SetAddr(addr string)
func (s *Server) SetAllowIps(ips []string)
func (s *Server) SetConfig(c ServerConfig)
func (s *Server) SetCookieDomain(domain string)
func (s *Server) SetCookieMaxAge(age int)
func (s *Server) SetCookiePath(path string)
func (s *Server) SetDenyIps(ips []string)
func (s *Server) SetDenyRoutes(routes []string)
func (s *Server) SetDumpRouteMap(enabled bool)
func (s *Server) SetErrorLogEnabled(enabled bool)
func (s *Server) SetGzipContentTypes(types []string)
func (s *Server) SetHTTPSAddr(addr string)
func (s *Server) SetHTTPSPort(port ...int)
func (s *Server) SetIdleTimeout(t time.Duration)
func (s *Server) SetIndexFiles(index []string)
func (s *Server) SetIndexFolder(index bool)
func (s *Server) SetLogHandler(handler LogHandler)
func (s *Server) SetLogPath(path string)
func (s *Server) SetMaxHeaderBytes(b int)
func (s *Server) SetNameToUriType(t int)
func (s *Server) SetPort(port ...int)
func (s *Server) SetReadTimeout(t time.Duration)
func (s *Server) SetServerAgent(agent string)
func (s *Server) SetServerRoot(root string)
func (s *Server) SetSessionIdName(name string)
func (s *Server) SetSessionMaxAge(age int)
func (s *Server) SetWriteTimeout(t time.Duration)
func (s *Server) Status() int
```


Web Server的配置比较丰富，所有的配置均可在创建`ghttp.Server`对象后使用`SetConfig`方法进行统一配置；也可以使用Server对象的`Set*/Enable*`方法进行特定配置的设置。主要注意的是，配置项在Server执行`Start`之后便不能再修改。

# 常用配置介绍

## 关闭路由表打印

在WebServer启动的时候默认会打印出当前注册的所有路由信息(包括HOOK注册)，这对于开发者来说非常有用。如果不想启动时打印路由表信息，可以通过以下方式关闭：
```go
g.Server().SetDumpRouteMap(false)
```
此外，我们也可以通过以下方式获取路由表信息(不自动打印)，随后我们可以自定义处理：
```go
routes := g.Server().GetRouteMap()
```








