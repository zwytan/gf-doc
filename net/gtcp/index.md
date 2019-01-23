TCPServer通过```gtcp.Server```实现。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gtcp"
```

接口文档：
[godoc.org/github.com/gogf/gf/g/net/gtcp#Server](https://godoc.org/github.com/gogf/gf/g/net/gtcp)
```go
type Server
    func GetServer(name ...interface{}) *Server
    func NewServer(address string, handler func(*Conn), names ...string) *Server
    func (s *Server) Run() error
    func (s *Server) SetAddress(address string)
    func (s *Server) SetHandler(handler func(*Conn))
```

其中```GetServer```使用单例模式通过给定一个唯一的名称获取/创建一个Server，后续可通过```SetAddress```和```SetHandler```方法动态修改Server属性；```NewServer```则直接根据给定参数创建一个Server对象。

我们通过实现一个简单的echo服务器来演示TCPServer的使用：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/net/gtcp"
)

func main() {
    gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
        defer conn.Close()
        for {
            data, err := conn.Recv(-1)
            if len(data) > 0 {
                if err := conn.Send(append([]byte("> "), data...)); err != nil {
                  fmt.Println(err)
                }
            }
            if err != nil {
                break
            }
        }
    }).Run()
}
```
在这个示例中我们使用了gtcp提供的两个工具方法```Send```和```Recv```来发送和接收数据。其中```Recv```方法会通过阻塞方式接收数据，直到客户端"发送完毕一条数据"(执行一次```Send```，底层Socket通信不带缓冲实现)，或者关闭链接。关于其中的链接对象```gtcp.Conn```的介绍，请继续阅读后续章节。

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

