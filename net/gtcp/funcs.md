`gtcp`模块也提供了一些常用的工具方法。

使用方式：
```go
import "gitee.com/johng/gf/g/net/gtcp"
```

方法列表：
[godoc.org/github.com/johng-cn/gf/g/net/gtcp](https://godoc.org/github.com/johng-cn/gf/g/net/gtcp)
```go
func Checksum(buffer []byte) uint32
func NewNetConn(addr string, timeout ...int) (net.Conn, error)
func Send(addr string, data []byte, retry ...Retry) error
func SendRecv(addr string, data []byte, receive int, retry ...Retry) ([]byte, error)
func SendRecvWithTimeout(addr string, data []byte, receive int, timeout time.Duration, retry ...Retry) ([]byte, error)
func SendWithTimeout(addr string, data []byte, timeout time.Duration, retry ...Retry) error
```

1. ```Checksum```常用于TCP通信时的数据校验和生成，便于通信两端进行校验和校验；
2. ```NewNetConn```用于简化标准库连接对象```net.Conn```的创建；
3. ```Send*```系列方法直接通过给定地址进行数据发送，用于短链接请求的情况；

以下为一个简单的示例，我们使用工具方法来访问指定的Web站点：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/net/gtcp"
)

func main() {
    data, err := gtcp.SendRecv("www.baidu.com:80", []byte("GET / HTTP/1.1\n\n"), -1)
    if err != nil {
        panic(err)
    }
    fmt.Println(string(data))
}
```
在这个示例中，我们通过TCP访问百度首页，模拟HTTP请求头信息，并获得返回结果。
执行后，输出结果如下：
```html
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: no-cache
Connection: Keep-Alive
Content-Length: 14615
Content-Type: text/html
Date: Fri, 20 Jul 2018 05:45:11 GMT
Etag: "5b3c3650-3917"
Last-Modified: Wed, 04 Jul 2018 02:52:00 GMT
P3p: CP=" OTI DSP COR IVA OUR IND COM "
Pragma: no-cache
Server: BWS/1.1
...
(略)
```
