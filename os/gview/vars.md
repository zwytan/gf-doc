[TOC]

# 变量对象

我们可以在模板中使用对象，并可在模板中访问对象的属性及调用其方法。

示例：

```go
package main

import (
    "github.com/gogf/gf/g"
)

type T struct {
    Name string
}

func (t *T) Hello(name string) string {
    return "Hello " + name
}

func (t *T) Test() string {
    return "This is test"
}

func main() {
    t := &T{"John"}
    v := g.View()
    content := `{{.t.Hello "there"}}, my name's {{.t.Name}}. {{.t.Test}}.`
    if r, err := v.ParseContent(content, g.Map{"t" : t}); err != nil {
        g.Dump(err)
    } else {
        g.Dump(r)
    }
}
```
其中，赋值给模板的变量既可以是`对象指针`也可以是`对象变量`。但是注意定义的对象方法，如果为对象指针那么只能调用方法接收器为对象指针的方法；如果为对象变量，那么只能调用方法接收器为对象的方法。

执行后，输出结果为：
```html
Hello there, my name's John. This is test.
```




# 内置变量

## `ghttp`内置变量

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
    "github.com/gogf/gf/g"
    "github.com/gogf/gf/g/net/ghttp"
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