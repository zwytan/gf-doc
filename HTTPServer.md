### Web Server
简单示例：
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

### 多域名支持