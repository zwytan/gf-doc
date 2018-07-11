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

我们通过一个例子来看看如何使用```gtcp.Conn```对象。

1. **TCPServer**

    ```go
    package main

    import (
        "net"
        "gitee.com/johng/gf/g/os/glog"
        "gitee.com/johng/gf/g/net/gtcp"
    )

    func main() {
        gtcp.NewServer(":8999", func(conn net.Conn) {
            c := gtcp.NewConnByNetConn(conn)
            defer c.Close()
            for {
                if data, err := c.Receive(); err == nil {
                    glog.Println(string(data))
                }
            }
        }).Run()
    }
    ```
    Server端的功能很简单，接收到客户端的数据后立即打印到终端上。
    
1. **TCPClient**

    ```go
    package main

    import (
        "gitee.com/johng/gf/g/net/gtcp"
        "gitee.com/johng/gf/g/util/gconv"
        "gitee.com/johng/gf/g/os/glog"
        "time"
    )

    func main() {
        conn, err := gtcp.NewConn("127.0.0.1:8999")
        if err != nil {
            glog.Fatal(err)
        }
        for i := 0; i < 10000; i++ {
            if err := conn.Send([]byte(gconv.String(i))); err != nil {
                glog.Error(err)
            }
            time.Sleep(time.Second)
        }
    }
    ```
    Client的功能也很简单，使用同一个连接对象，在循环中每隔1秒向服务端发送当前递增的数字。同时，该功能也可以演示出底层Socket通信并没有使用缓冲实现，也就是说，执行一次```Send```即立刻向服务端发送数据。因此，客户端需要在本地自行管理好需要发送的缓冲数据。
    
1. **执行结果**

	先执行Server端，随后再执行Client端，执行后，可以看到Server端在终端上输出以下信息：
    ```shell
    2018-07-11 22:11:08.650 0
    2018-07-11 22:11:09.651 1
    2018-07-11 22:11:10.651 2
    2018-07-11 22:11:11.651 3
    2018-07-11 22:11:12.651 4
    2018-07-11 22:11:13.651 5
    2018-07-11 22:11:14.652 6
    2018-07-11 22:11:15.652 7
    2018-07-11 22:11:16.652 8
    2018-07-11 22:11:17.652 9
    2018-07-11 22:11:18.652 10
    2018-07-11 22:11:19.653 11
    ...
    ```