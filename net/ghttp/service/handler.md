
[TOC]

# 回调函数注册

**回调函数+执行对象混合注册是推荐的服务注册方式。**

这种方式是最简单且最灵活的的服务注册方式，注册的服务可以是一个实例化对象的方法地址，也可以是一个包方法地址。服务需要的数据可以通过```包内部变量形式```或者```对象内部变量形式```进行管理，开发者可根据实际情况进行灵活控制。

我们可以直接通过```ghttp.BindHandler```方法完成回调函数的注册，在框架的开发手册中很多地方都使用了回调函数注册的方式来做演示，因为这种注册方式比较简单。



## 使用示例1，与执行对象混合使用
[gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/product.go](https://gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/product.go)
```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Product struct {
    total int
}

func init() {
    p := &Product{}
    g.Server().BindHandler("/product/total", p.Total)
    g.Server().BindHandler("/product/list/{page}.html", p.List)
}

func (p *Product) Total(r *ghttp.Request) {
    p.total++
    r.Response.Write("total: ", p.total)
}

func (p *Product) List(r *ghttp.Request) {
    r.Response.Write("page: ", r.Get("page"))
}
```
在这个示例中，我们使用对象来封装业务逻辑和所需的变量，使用回调函数注册来灵活注册对应的对象方法。

## 使用示例2，使用包变量管理内部变量
[gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/stats/stats.go](https://gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/stats/stats.go)
```go
package stats

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

var (
    total int
)

func init() {
    g.Server().BindHandler("/stats/total", showTotal)
}

func showTotal(r *ghttp.Request) {
    total++
    r.Response.Write("total:", total)
}
```

## 使用示例3，请求参数绑定到struct对象
[gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_struct.go](https://gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_struct.go)
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type User struct {
    Uid   int    `json:"uid"`
    Name  string `json:"name"  params:"username"`
    Pass1 string `json:"pass1" params:"password1,userpass1"`
    Pass2 string `json:"pass2" params:"password3,userpass2"`
}

func main() {
    s := g.Server()
    s.BindHandler("/user", func(r *ghttp.Request){
        user := new(User)
        r.GetToStruct(user)
        //r.GetPostToStruct(user)
        //r.GetQueryToStruct(user)
        r.Response.WriteJson(user)
    })
    s.SetPort(8199)
    s.Run()
}
```
可以看到，我们可以在定义struct的时候使用```params```的标签来指定匹配绑定的参数名称，并且支持多个参数名称的绑定，多个参数名称以```,```号分隔。在使用中我们可以使用```GetRequestToStruct/GetPostToStruct/GetQueryToStruct```三种方式来获得指定提交方式的参数map，此外```GetToStruct```是```GetRequestToStruct```的别名，大多数情况下我们使用该方式获取参数即可。

如果是其他方式提交参数，如果Json/Xml等等，由于设计到自定义参数格式的解析再绑定，请参考```gconv```包map转换struct的标签名称```gconv```的[用法示例](util/gconv/index)。

以上示例执行后，我们手动访问地址```http://127.0.0.1:8199/user?uid=1&name=john&password1=123&userpass2=123```，输出结果为：
```json
{"name":"john","pass1":"123","pass2":"123","uid":1}
```

## 使用示例4，请求参数绑定+数据校验示例
[gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_validation.go](https://gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_validation.go)
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/util/gvalid"
    "gitee.com/johng/gf/g/encoding/gparser"
)

func main() {
    type User struct {
        Uid   int    `gvalid:"uid@min:1"`
        Name  string `params:"username"  gvalid:"username @required|length:6,30"`
        Pass1 string `params:"password1" gvalid:"password1@required|password3"`
        Pass2 string `params:"password2" gvalid:"password2@required|password3|same:password1#||两次密码不一致，请重新输入"`
    }

    s := g.Server()
    s.BindHandler("/user", func(r *ghttp.Request){
        user := new(User)
        r.GetToStruct(user)
        result  := gvalid.CheckStruct(user, nil)
        json, _ := gparser.VarToJsonIndent(result)
        r.Response.Write(json)
    })
    s.SetPort(8199)
    s.Run()
}
```
其中，```gvalid```标签为```gavlid```数据校验包特定的校验规则标签，具体请参考【[数据校验](util/gvalid/index)】章节。此外，为了便于查看返回的json结果，这里在示例中使用```gparser```包的```VarToJsonIndent```方法，平常开发中往往直接使用像上一个示例中使用的```r.Response.WriteJson```方法返回json结果即可。执行后，访问URL```http://127.0.0.1:8199/user?uid=1&name=john&password1=123&password2=456```，输出结果为：
```json
{
	"password1": {
		"password3": "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符"
	},
	"password2": {
		"password3": "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符",
		"same": "字段值不合法"
	},
	"username": {
		"length": "字段长度为6到30个字符"
	}
}
```









