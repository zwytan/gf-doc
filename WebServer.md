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
使用ghttp的GetServer()方法可以创建一个Web Server，该方法采用单例模式设计，也就是说，多次调用该方法，返回的是同一个Web Server对象。

创建了Web Server对象之后，我们可以使用对应的各种Set方法来设置Web Server的属性。
1. SetPort 用来设置Web Server的监听端口（默认为80）；
2. SetIndexFolder 用来设置是否允许列出Web Server主目录的文件列表（默认为false）；
3. SetServerRoot 用来设置Web Server的主目录（默认为空，在某些时候，Web Server仅提供接口服务，因此Web Server的主目录为非必需参数）；



### 多域名支持