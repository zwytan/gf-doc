[TOC]

# 内置变量

## WebServer内置变量

以下内置模板变量仅在`WebServer`下，即通过`ghttp`模块使用`ghttp.Response`/`gmvc.View`对象渲染模板引擎时有效。

### Config
访问默认的配置管理(`config.toml`)对象Map值。

使用方式：
```go
{{.Config.配置项路径}}
```

### Cookie
访问当前请求的`Cookie`对象Map值。

使用方式：
```go
{{.Cookie.键名}}
```

### Session
访问当前请求的`Session`对象Map值。

使用方式：
```go
{{.Session.键名}}
```

使用示例：
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindHandler("/", func(r *ghttp.Request){
        r.Cookie.Set("theme", "default")
        r.Session.Set("name", "john")
        content :=`Config:{{.Config.redis.cache}}, Cookie:{{.Cookie.theme}}, Session:{{.Session.name}}`
        r.Response.WriteTplContent(content, nil)
    })
    s.SetPort(8199)
    s.Run()
}
```

其中，`config.toml`内容为：
```toml
# Redis数据库配置
[redis]
    disk  = "127.0.0.1:6379,0"
    cache = "127.0.0.1:6379,1"
```

执行后，访问`http://127.0.0.1:8199/`，输出结果为：
```html
Config:127.0.0.1:6379,1, Cookie:default, Session:john
```