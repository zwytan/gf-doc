```gtcp.PoolConn```采用了连接池实现，连接池缓存固定时间为600秒，且内部实现了数据发送时的断开重连机制。我们接下来使用两个示例来演示一下连接池的作用。

**示例1**
```go
package main

import (
    "fmt"
    "time"
    "net"
    "gitee.com/johng/gf/g/net/gtcp"
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/os/glog"
)

func main() {
    // Server
    go gtcp.NewServer("127.0.0.1:8999", func(conn net.Conn) {
        c := gtcp.NewConnByNetConn(conn)
        defer c.Close()
        for {
            if data, _ := c.Receive(); len(data) > 0 {
                conn.Write(append([]byte("> "), data...))
            }
            // 如果接收并发送返回数据后return，表示关闭连接
            // return
        }
    }).Run()

    time.Sleep(time.Second)

    // Client
    for {
       if conn, err := gtcp.NewConn("127.0.0.1:8999"); err == nil {
           b, err := conn.SendReceive([]byte(gtime.Datetime()))
           if len(b) > 0 {
               fmt.Println(string(b), conn.LocalAddr(), conn.RemoteAddr())
               conn.Close()
           }
           // 如果错误是EOF，表示对方关闭连接
           if err != nil {
               fmt.Println(err)
           }
       } else {
           glog.Error(err)
       }
       time.Sleep(time.Second)
    }
}
```
在这个示例中，我们将Server和Client写到一个文件中，其中Server创建新的goroutine异步运行，Client在main goroutine中执行。Server端是一个回显服务器，Client每隔1秒向Server端发送当前的时间，经过Server端回显返回后，在Client端打印出双方的连接端口信息。
执行后，结果如下：
```shell
> 2018-07-11 23:29:54 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:55 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:56 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:57 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:58 127.0.0.1:55876 127.0.0.1:8999
> ...
```
可以看到，Client的端口一直未变，每一次通过```gtcp.NewConn("127.0.0.1:8999")```获得的都是同一个```gtcp.Conn```对象，且每一次```conn.Close()```时并不是真正的关闭连接，而是将该对象重新丢回到连接池里循环使用。



**示例2**
这个例子是为了展示当服务端关闭连接后，该连接对象还是否有效的处理。


未完待续。