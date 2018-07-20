
[TOC]

gtcp包提供了非常简便易用的```gtcp.Conn```对象。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gtcp"
```

方法列表：
[godoc.org/github.com/johng-cn/gf/g/net/gtcp#Conn](https://godoc.org/github.com/johng-cn/gf/g/net/gtcp)
```go
type Conn
    func NewConn(addr string, timeout ...int) (*Conn, error)
    func NewConnByNetConn(conn net.Conn) *Conn
    func (c *Conn) Receive(length int, retry ...Retry) ([]byte, error)
    func (c *Conn) ReceiveWithTimeout(length int, timeout time.Duration, retry ...Retry) ([]byte, error)
    func (c *Conn) Send(data []byte, retry ...Retry) error
    func (c *Conn) SendReceive(data []byte, receive int, retry ...Retry) ([]byte, error)
    func (c *Conn) SendReceiveWithTimeout(data []byte, receive int, timeout time.Duration, retry ...Retry) ([]byte, error)
    func (c *Conn) SendWithTimeout(data []byte, timeout time.Duration, retry ...Retry) error
```
我们接下来通过通过几个例子来看看如何简单使用```gtcp.Conn```对象。

# 基本介绍

## 写入操作
TCP通信写入操作由```Send```方法实现，并提供了错误重试的机制，由第二个非必须参数提供。需要注意的是```Send```方法不带任何的缓冲机制，也就是说每调用一次```Send```方法将会立即调用底层的TCP Write方法写入数据(缓冲机制依靠系统底层实现)。因此，如果想要进行输出缓冲控制，请在业务层上进行处理。

在进行TCP写入时，可靠的通信场景下往往是一写一读，即```Send```成功之后接着便开始```Receive```获取对方的反馈结果。因此```gtcp.Conn```特提供了方便的```SendReceive```方法。


## 读取操作
TCP通信读取操作由```Receive```方法实现，同时也提供了宠物重试的机制，由第二个非必须参数提供。```Receive```方法提供了内置的读取缓冲控制，读取数据时可以指定读取的长度（由```length```参数指定），当读取到指定的长度的数据后后将会立即返回。如果```length <= 0```那么将会读取所有可读取的缓冲区数据并返回。

在大部分的场景中，由于TCP通信的时候需要约定固定的数据结构，因此读取数据的时候往往是指定长度读取。一般来说，第一次读取包的长度（例如：```Receive(4)```读取```4```字节的数据，作为包长度的值），解析后再根据读取的长度值（例如解析出来的长度值为```length=56```，那么下一次读取使用```Receive(length)```）读取下一次包的数据，这边便能更好地进行数据包读取和解析。

需要注意的是，使用```Receive(-1)```读取所有缓冲区可读数据时需要注意包的解析问题，容易产生非完整包的情况。这个时候，业务层需要根据既定的数据包结构自己负责包的完整性处理。


## 超时处理

```gtcp.Conn```对TCP通信时的数据写入和读取提供了超时处理，通过方法中的```timeout```参数指定，这块比较简单，不过多详解。



# 示例1，简单使用

```go
package main

import (
    "fmt"
    "time"
    "gitee.com/johng/gf/g/net/gtcp"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/util/gconv"
)

func main() {
    // Server
    go gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
        defer conn.Close()
        for {
            data, err := conn.Receive(-1)
            if len(data) > 0 {
                  fmt.Println(string(data))
            }
            if err != nil {
                break
            }
        }
    }).Run()

    time.Sleep(time.Second)

    // Client
    conn, err := gtcp.NewConn("127.0.0.1:8999")
    if err != nil {
        panic(err)
    }
    for i := 0; i < 10000; i++ {
        if err := conn.Send([]byte(gconv.String(i))); err != nil {
            glog.Error(err)
        }
        time.Sleep(time.Second)
    }
}
```
1. Server端的功能很简单，接收到客户端的数据后立即打印到终端上。
1. Client的功能也很简单，使用同一个连接对象，在循环中每隔1秒向服务端发送当前递增的数字。同时，该功能也可以演示出底层Socket通信并没有使用缓冲实现，也就是说，执行一次```Send```即立刻向服务端发送数据。因此，客户端需要在本地自行管理好需要发送的缓冲数据。
1. 执行结果
	执行后，可以看到Server端在终端上输出以下信息：
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
    
# 示例2，回显服务

我们将之前的回显服务改进一下：
```go
package main

import (
    "fmt"
    "time"
    "gitee.com/johng/gf/g/net/gtcp"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/os/gtime"
)

func main() {
    // Server
    go gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
        defer conn.Close()
        for {
            data, err := conn.Receive(-1)
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

    time.Sleep(time.Second)

    // Client
    for {
       if conn, err := gtcp.NewConn("127.0.0.1:8999"); err == nil {
           if b, err := conn.SendReceive([]byte(gtime.Datetime()), -1); err == nil {
               fmt.Println(string(b), conn.LocalAddr(), conn.RemoteAddr())
           } else {
               fmt.Println(err)
           }
           conn.Close()
       } else {
           glog.Error(err)
       }
       time.Sleep(time.Second)
    }
}
```
执行后，输出结果为：
```html
> 2018-07-19 23:25:43 127.0.0.1:34306 127.0.0.1:8999
> 2018-07-19 23:25:44 127.0.0.1:34308 127.0.0.1:8999
> 2018-07-19 23:25:45 127.0.0.1:34312 127.0.0.1:8999
> 2018-07-19 23:25:46 127.0.0.1:34314 127.0.0.1:8999
```

