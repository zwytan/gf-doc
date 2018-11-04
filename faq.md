[TOC]

# Web服务开发

## 1. 路由注册中: 路由`/`与`/*`的区别

- `/`仅匹配URI为`/`时的路由，这是一个`精准匹配规则`；
- `/*`匹配所有的路由，是`模糊匹配规则`，并且在优先级的控制下，该路由的优先级最低，在其他路由都无法匹配的情况下，最后会使用`/*`路由；

## 2. 在`init`包初始化方法中执行服务注册，但是却访问不到注册的路由
我按照文档上面的示例进行执行对象注册，但是运行后访问注册的路由（`http://127.0.0.1/object`）提醒我`404`，好着急啊，我在线等。
```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func init() {
    g.Server().BindObject("/object", new(Object))
}

func (o *Object) Index(r *ghttp.Request) {
    r.Response.Write("object index")
}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Write("object show")
}
```

回答：

1. 如何判断路由是否执行成功？在程序运行后终端会打印出当前注册的路由表信息，检查是否注册成功。
1. 该问题是没有在`main`包中引入`demo`包造成的，引入方式:
    ```go
    import _ "PATH/TO/YOUR/PROJECT/PACKAGE"
    ```
    文档链接: 【[服务注册-基本介绍](net/ghttp/service/index.md)】



# 数据库ORM

## 1. 数据库查询结果转`json`没有了数值

将数据库数据集进行`json`编码但是返回结果中没有值，如：
```go
r, _ := db.Table("user").Where("uid=?", 1).One()
if r != nil {
    b, _ := gjson.Encode(r)
    fmt.Println(string(b))
}
```
结果为：
```json
{"email":{},"name":{},"type":{},"uid":{}}
```

回答：

1. 标准库原生的结果集数值为`[]byte`类型，`gform`为了方便开发者转换数据格式，将结果集数值改进为了`gvar.Var`对象，因此在以上结果中看到的数值为一个对象的`{}`；
1. 以上代码中我们可以方便地通过`r.ToJson()`来进行数据格式转换，当然`gform`的结果集支持多种数据格式转换，详细请参考【[ORM高级特性](database/orm/senior)】章节；
1. `gvar.Var`对象的详细介绍请参考【[gvar通用动态变量](container/gvar/index)】章节；







