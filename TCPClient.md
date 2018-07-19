
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

## TCP基础

## 写入操作

## 读取操作

## 超时处理

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

