gtcp包提供了非常简便易用的```gtcp.Conn```对象。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gtcp"
```

方法列表：
[godoc.org/github.com/johng-cn/gf/g/net/gtcp#Conn](https://godoc.org/github.com/johng-cn/gf/g/net/gtcp)
```go
type Conn
    func NewConn(ip string, port int, timeout ...int) (*Conn, error)
    func NewConnByNetConn(conn net.Conn) *Conn
    func (c *Conn) Close() error
    func (c *Conn) Receive(retry ...Retry) ([]byte, error)
    func (c *Conn) ReceiveWithTimeout(timeout time.Duration, retry ...Retry) ([]byte, error)
    func (c *Conn) Send(data []byte, retry ...Retry) error
    func (c *Conn) SendReceive(data []byte, retry ...Retry) ([]byte, error)
    func (c *Conn) SendReceiveWithTimeout(data []byte, timeout time.Duration, retry ...Retry) ([]byte, error)
    func (c *Conn) SendWithTimeout(data []byte, timeout time.Duration, retry ...Retry) error
```

>[danger] # TCPServer

我们