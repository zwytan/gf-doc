TCPServer通过gtcp包提供支持，以下是方法列表：
```go
func GetServer(names ...string) *Server
func NewServer(address string, handler func(net.Conn), names ...string) *Server
func (s *Server) Run() error
func (s *Server) SetAddress(address string)
func (s *Server) SetHandler(handler func(net.Conn))
```

我们通过实现一个简单的echo服务器来演示TCPServer的使用：

https://gitee.com/johng/gf/blob/master/geg/net/tcp_server.go

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

执行之后使用telnet进行测试：

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