[TOC]

# 请求输入

请求输入依靠 `ghttp.Request` 对象实现：
```go
// 请求对象
type Request struct {
    http.Request
    parsedGet     bool                    // GET参数是否已经解析
    parsedPost    bool                    // POST参数是否已经解析
    queryVars     map[string][]string     // GET参数
    routerVars    map[string][]string     // 路由解析参数
    exit          bool                    // 是否退出当前请求流程执行
    Id            int                     // 请求id(唯一)
    Server        *Server                 // 请求关联的服务器对象
    Cookie        *Cookie                 // 与当前请求绑定的Cookie对象(并发安全)
    Session       *Session                // 与当前请求绑定的Session对象(并发安全)
    Response      *Response               // 对应请求的返回数据操作对象
    Router        *Router                 // 匹配到的路由对象(只有匹配到才会有，仅在服务回调中有效)
    EnterTime     int64                   // 请求进入时间(微秒)
    LeaveTime     int64                   // 请求完成时间(微秒)
    params        map[string]gvar.VarRead // 开发者自定义参数(请求流程中有效)
    parsedHost    string                  // 解析过后不带端口号的服务器域名名称
    clientIp      string                  // 解析过后的客户端IP地址
    isFileRequest bool                    // 是否为静态文件请求(非服务请求，当静态文件存在时，优先级会被服务请求高，被识别为文件请求)
    isFileServe   bool                    // 是否为文件处理(调用Server.serveFile时设置为true), isFileRequest为true时isFileServe也为true
}
```
`ghttp.Request`继承了底层的`http.Request`对象，并且包含了会话相关的`Cookie`和`Session`对象(每个请求都会有两个**独立**的`Cookie`和`Session对象`)。此外，每个请求有一个`唯一的Id`（请求Id，全局唯一），用以标识每一个请求。此外，成员对象包含一个与当前请求对应的返回输出对象指针Response，用于数据的返回。

相关方法（API详见： [godoc.org/github.com/johng-cn/gf/g/net/ghttp#Request](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)）：
```go
type Request
    func (r *Request) AddPost(key string, value string)
    func (r *Request) AddQuery(key string, value string)
    func (r *Request) AddRouterString(key, value string)
    func (r *Request) BasicAuth(user, pass string, tips ...string) bool
    func (r *Request) Exit()
    func (r *Request) IsAjaxRequest() bool
    func (r *Request) IsExited() bool
    func (r *Request) IsFileRequest() bool
    func (r *Request) SetPost(key string, value string)
    func (r *Request) SetQuery(key string, value string)
    func (r *Request) SetRouterString(key, value string)
    func (r *Request) WebSocket() (*WebSocket, error)

    func (r *Request) SetParam(key string, value interface{})
    func (r *Request) GetParam(key string) gvar.VarRead

    func (r *Request) Get(key string, def ...string) string
    func (r *Request) GetVar(key string, def ...interface{}) gvar.VarRead
    func (r *Request) GetArray(key string, def ...[]string) []string
    func (r *Request) GetClientIp() string
    func (r *Request) GetFloat32(key string, def ...float32) float32
    func (r *Request) GetFloat64(key string, def ...float64) float64
    func (r *Request) GetFloats(key string, def ...[]float64) []float64
    func (r *Request) GetHost() string
    func (r *Request) GetInt(key string, def ...int) int
    func (r *Request) GetInterfaces(key string, def ...[]interface{}) []interface{}
    func (r *Request) GetInts(key string, def ...[]int) []int
    func (r *Request) GetJson() *gjson.Json
    func (r *Request) GetMap(def ...map[string]string) map[string]string
    func (r *Request) GetRaw() []byte
    func (r *Request) GetReferer() string
    func (r *Request) GetString(key string, def ...string) string
    func (r *Request) GetStrings(key string, def ...[]string) []string
    func (r *Request) GetToStruct(object interface{}, mapping ...map[string]string)
    func (r *Request) GetUint(key string, def ...uint) uint
    
    func (r *Request) GetPost(key string, def ...[]string) []string
    func (r *Request) GetPostArray(key string, def ...[]string) []string
    func (r *Request) GetPostBool(key string, def ...bool) bool
    func (r *Request) GetPostFloat32(key string, def ...float32) float32
    func (r *Request) GetPostFloat64(key string, def ...float64) float64
    func (r *Request) GetPostFloats(key string, def ...[]float64) []float64
    func (r *Request) GetPostInt(key string, def ...int) int
    func (r *Request) GetPostInterfaces(key string, def ...[]interface{}) []interface{}
    func (r *Request) GetPostInts(key string, def ...[]int) []int
    func (r *Request) GetPostMap(def ...map[string]string) map[string]string
    func (r *Request) GetPostString(key string, def ...string) string
    func (r *Request) GetPostStrings(key string, def ...[]string) []string
    func (r *Request) GetPostToStruct(object interface{}, mapping ...map[string]string)
    func (r *Request) GetPostUint(key string, def ...uint) uint

    func (r *Request) GetQuery(key string, def ...[]string) []string
    func (r *Request) GetQueryArray(key string, def ...[]string) []string
    func (r *Request) GetQueryBool(key string, def ...bool) bool
    func (r *Request) GetQueryFloat32(key string, def ...float32) float32
    func (r *Request) GetQueryFloat64(key string, def ...float64) float64
    func (r *Request) GetQueryFloats(key string, def ...[]float64) []float64
    func (r *Request) GetQueryInt(key string, def ...int) int
    func (r *Request) GetQueryInterfaces(key string, def ...[]interface{}) []interface{}
    func (r *Request) GetQueryInts(key string, def ...[]int) []int
    func (r *Request) GetQueryMap(def ...map[string]string) map[string]string
    func (r *Request) GetQueryString(key string, def ...string) string
    func (r *Request) GetQueryStrings(key string, def ...[]string) []string
    func (r *Request) GetQueryToStruct(object interface{}, mapping ...map[string]string)
    func (r *Request) GetQueryUint(key string, def ...uint) uint

    func (r *Request) GetRequest(key string, def ...[]string) []string
    func (r *Request) GetRequestArray(key string, def ...[]string) []string
    func (r *Request) GetRequestBool(key string, def ...bool) bool
    func (r *Request) GetRequestFloat32(key string, def ...float32) float32
    func (r *Request) GetRequestFloat64(key string, def ...float64) float64
    func (r *Request) GetRequestFloats(key string, def ...[]float64) []float64
    func (r *Request) GetRequestInt(key string, def ...int) int
    func (r *Request) GetRequestInterfaces(key string, def ...[]interface{}) []interface{}
    func (r *Request) GetRequestInts(key string, def ...[]int) []int
    func (r *Request) GetRequestMap(def ...map[string]string) map[string]string
    func (r *Request) GetRequestString(key string, def ...string) string
    func (r *Request) GetRequestStrings(key string, def ...[]string) []string
    func (r *Request) GetRequestToStruct(object interface{}, mapping ...map[string]string)
    func (r *Request) GetRequestUint(key string, def ...uint) uint
    func (r *Request) GetRequestVar(key string, def ...interface{}) *gvar.Var

    func (r *Request) GetRouterArray(key string) []string
    func (r *Request) GetRouterString(key string) string
```
以上方法可以分为以下几类：
1. `Get*`: 常用方法，简化参数获取，```GetRequest*```的别名；
1. `GetQuery*`: 获取GET方式传递过来的Key-Value查询参数；
2. `GetPost*`: 获取POST方式传递过来的Key-Value表单参数；
3. `GetRequest*`: 优先查找Router路由参数中是否有指定键名的参数，如果没有则查找GET参数，如果没有则查找POST参数，如果都不存在则返回空或者默认值；
4. `GetRaw`: 获取客户端提交的原始数据(二进制`[]byte`类型)，与HTTP Method无关，例如客户端提交JSON/XML数据格式时可以通过该方法获取原始的提交数据；
5. `GetJson`: 自动将原始请求信息解析为`gjson.Json`对象指针返回，`gjson.Json`对象指针具体在【[gjson模块](encoding/gjson/index.md)】章节中介绍；
1. `GetToStruct`: 将请求参数绑定到指定的struct对象上，注意给定的参数为对象指针；
6. `SetParam`/`GetParam`: 用于设置/获取请求流程中得共享变量，该共享变量只在该请求流程中有效，请求结束则销毁；

其中，获取的参数方法可以对指定键名的数据进行自动类型转换，例如：`http://127.0.0.1:8199/?amount=19.66`，通过`Get`/`GetQueryString`将会返回`19.66`的字符串类型，`GetQueryFloat32`/`GetQueryFloat64`将会分别返回`float32`和`float64`类型的数值`19.66`。但是，`GetQueryInt`/`GetQueryUint`将会返回`19`（如果参数为float类型的字符串，将会按照**向下取整**进行整型转换）。

## `Request.URL`与`Request.Router`

`Request.Router`是匹配到的路由对象，包含路由注册信息，一般来说开发者不会用得到。
`Request.URL`是底层请求的URL对象（继承自标准库`http.Request`），包含请求的URL地址信息，特别是`Request.URL.Path`表示请求的URI地址。

因此，假如在服务回调函数中使用的话，`Request.Router`是有值的，因为只有匹配到了路由才会调用服务回调方法。但是在事件回调函数中，该对象可能为`nil`（表示没有匹配到服务回调函数路由）。特别是在使用时间回调对请求接口鉴权的时候，应当使用`Request.URL`对象获取请求的URL信息，而不是`Request.Router`。

# 使用示例

## 示例1，流程共享变量
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 优先调用的HOOK
func beforeServeHook1(r *ghttp.Request) {
    r.SetParam("name", "GoFrame")
    r.Response.Writeln("set name")
}

// 随后调用的HOOK
func beforeServeHook2(r *ghttp.Request) {
    r.SetParam("site", "https://gfer.me")
    r.Response.Writeln("set site")
}

// 允许对同一个路由同一个事件注册多个回调函数，按照注册顺序进行优先级调用。
// 为便于在路由表中对比查看优先级，这里讲HOOK回调函数单独定义为了两个函数。
func main() {
    s := g.Server()
    s.BindHandler("/", func(r *ghttp.Request) {
        r.Response.Writeln(r.GetParam("name").String())
        r.Response.Writeln(r.GetParam("site").String())
    })
    s.BindHookHandler("/", ghttp.HOOK_BEFORE_SERVE, beforeServeHook1)
    s.BindHookHandler("/", ghttp.HOOK_BEFORE_SERVE, beforeServeHook2)
    s.SetPort(8199)
    s.Run()
}
```
执行后，访问 [http://127.0.0.1:8199/](http://127.0.0.1:8199/) 后，页面输出内容为：
```
set name
set site
GoFrame
https://gfer.me
```


## 示例2，请求数据校验

### 请求参数绑定+数据校验示例
[gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_validation.go](https://gitee.com/johng/gf/blob/master/geg/net/ghttp/server/request/request_validation.go)
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/util/gvalid"
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
        if err := gvalid.CheckStruct(user, nil); err != nil {
            r.Response.WriteJson(err.Maps())
        } else {
            r.Response.Write("ok")
        }
    })
    s.SetPort(8199)
    s.Run()
}
```
其中，
1. 这里使用了`r.GetToStruct(user)`方法将请求参数绑定到指定的`user`对象上，注意这里的`user`是`User`的结构体实例化指针；在struct tag中，使用`params`标签来指定参数名称与结构体属性名称对应关系；
1. 通过`gvalid`标签为`gavlid`数据校验包特定的校验规则标签，具体请参考【[数据校验](util/gvalid/index.md)】章节；其中，密码字段的校验规则为`password3`，表示: `密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符`；
1. 当校验染回结果非`nil`时，表示校验不通过，这里使用`r.Response.WriteJson`方法返回json结果；

执行后，试着访问URL: [http://127.0.0.1:8199/user?uid=1&name=john&password1=123&password2=456](http://127.0.0.1:8199/user?uid=1&name=john&password1=123&password2=456)，输出结果为：
```json
{"password1":{"password3":"密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符"},"password2":{"password3":"密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符","same":"两次密码不一致，请重新输入"},"username":{"length":"字段长度为6到30个字符"}}
```


假如我们访问访问URL: [http://127.0.0.1:8199/user?uid=1&name=johng-cn&password1=John$123&password2=John$123](http://127.0.0.1:8199/user?uid=1&name=johng-cn&password1=John$123&password2=John$123)，输出结果为：
```
ok
```