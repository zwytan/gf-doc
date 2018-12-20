
# 服务配置

## go.mod
`/go.mod`
```
module gitee.com/johng/gf-demos

require gitee.com/johng/gf latest
```
其中注意`module`名称设置为`gitee.com/johng/gf-demos`。


## 配置文件
`/config/config.toml`
```toml
# 数据库连接
[database]
    [[database.default]]
        host = "127.0.0.1"
        port = "3306"
        user = "root"
        pass = "12345678"
        name = "test"
        type = "mysql"
```

## 启动设置
`/boot/boot.go`
```go
package boot

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 用于应用初始化。
func init() {
    s := g.Server()
    s.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_ALLLOWER)
    s.SetErrorLogEnabled(true)
    s.SetAccessLogEnabled(true)
    s.SetPort(8199)
}
```


