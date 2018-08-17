```gudp```包也提供了一些常用的工具方法。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gudp"
```

方法列表：
[godoc.org/github.com/johng-cn/gf/g/net/gudp](https://godoc.org/github.com/johng-cn/gf/g/net/gudp)
```go
func NewNetConn(raddr string, laddr ...string) (*net.UDPConn, error)
func Send(addr string, data []byte, retry ...Retry) error
func SendRecv(addr string, data []byte, receive int, retry ...Retry) ([]byte, error)
```

gudp的工具相对比较简单。其中，```NewNetConn```方法用于创建标准库的```net.UDPConn```通信对象；```Send```与```SendRecv```用于根据给定的UDP Server地址直接地进行UDP通信，数据写入及读取。