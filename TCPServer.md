
TCPServer通过gtcp包提供支持，以下是方法列表：
```go
func GetServer(names ...string) *Server
func NewServer(address string, handler func(net.Conn), names ...string) *Server
func (s *Server) Run() error
func (s *Server) SetAddress(address string)
func (s *Server) SetHandler(handler func(net.Conn))
```

来一个简单的示例：
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
                conn.Write(append([]byte("What you send, what you receive: "), buffer[0 : length]...))
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
What you send, what you receive: hello
hi there
What you send, what you receive: hi there
```