
# 服务配置

我们使用`go.mod`来管理项目依赖。

## go.mod
`/go.mod`
```
module gitee.com/johng/gf-demos

require gitee.com/johng/gf latest
```
其中注意`module`名称设置为`gitee.com/johng/gf-demos`。


## 配置文件
这里主要配置了数据库的连接信息。

`/config/config.toml`
```toml
# 应用系统设置
[setting]
    logpath = "/tmp/log/gf-demos"

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
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 用于应用初始化。
func init() {
    v := g.View()
    c := g.Config()
    s := g.Server()

    // 配置对象及视图对象配置
    c.AddPath("config")
    v.AddPath("template")
    v.SetDelimiters("${", "}")

    // glog配置
    logpath := c.GetString("setting.logpath")
    glog.SetPath(logpath)
    glog.SetStdPrint(true)

    // Web Server配置
    s.SetServerRoot("public")
    s.SetLogPath(logpath)
    s.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_ALLLOWER)
    s.SetErrorLogEnabled(true)
    s.SetAccessLogEnabled(true)
    s.SetPort(8199)
}
```


