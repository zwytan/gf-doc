TCPServer通过```gtcp.Server```实现。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gtcp"
```

方法列表：
[godoc.org/github.com/johng-cn/gf/g/net/gtcp#Server](https://godoc.org/github.com/johng-cn/gf/g/net/gtcp)
```go
type Server
    func GetServer(name ...interface{}) *Server
    func NewServer(address string, handler func(net.Conn), names ...string) *Server
    func (s *Server) Run() error
    func (s *Server) SetAddress(address string)
    func (s *Server) SetHandler(handler func(net.Conn))
```

其中```GetServer```使用单例模式通过给定一个唯一的名称获取/创建一个Server，后续可通过```SetAddress```和```SetHandler```方法动态修改Server属性；```NewServer```则直接根据给定参数创建一个Server对象。

我们通过实现一个简单的echo服务器来演示TCPServer的使用：

gitee.com/johng/gf/blob/master/geg/net/tcp_server.go

```go
package main

import (
    "net"
    "gitee.com/johng/gf/g/net/gtcp"
)

func main() {
    gtcp.NewServer(":8999", func(conn net.Conn) {
        for {
            buffer := make([]byte, 1024)
            if length, err := conn.Read(buffer); err == nil {
                conn.Write(append([]byte("> "), buffer[0 : length]...))
            }
        }
    }).Run()
}
```

执行之后我们使用```telnet```工具来进行测试：

```shell
john@home:~$ telnet 127.0.0.1 8999
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
hello        
> hello
hi there
> hi there
```

每一个客户端发起的TCP链接，TCPServer都会创建一个goroutine进行处理，直至TCP链接断开。由于goroutine比较轻量级，因此可以支撑比较大的并发量。