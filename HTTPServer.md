### Web Server
创建并运行一个Web Server：
```go
package main
import (
    "gitee.com/johng/gf/g/net/ghttp"
)
func main() {
    s := ghttp.GetServer()
    s.SetPort(8199)
    s.SetIndexFolder(true)
    s.SetServerRoot("/tmp")
    s.Run()
}
```
使用ghttp.GetServer()方法可以创建一个Web Server，该方法采用单例模式设计，也就是说，多次调用该方法，返回的是同一个Web Server对象。

创建了Web Server对象之后，我们可以使用对应的各种Set方法来设置Web Server的属性。这里我们通过SetPort设置了Web Server的监听端口，SetIndexFolder用来设置是否是否可以列出Web Server主目录的文件列表，SetServerRoot用来设置Web Server的主目录。
### 多服务支持


### 多域名支持