### Web Server
创建并运行一个Web Server：
```go
package main
import (
    "gitee.com/johng/gf/g/net/ghttp"
)
func main() {
    s := ghttp.GetServer()
    s.SetAddr(":8199")
    s.SetIndexFolder(true)
    s.SetServerRoot("/tmp")
    s.Run()
}
```
使用ghttp.GetServer()方法可以创建一个Web Server，该方法采用单例模式设计，也就是说，多次调用该方法，返回的是同一个Web Server对象。

创建了Web Server对象之后，我们可以使用对应的各种Set方法来设置Server的各种属性
### 多服务支持


### 多域名支持