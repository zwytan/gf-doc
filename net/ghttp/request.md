# 请求输入

请求输入依靠 `ghttp.Request` 对象实现：
```go
// 客户端请求对象
type Request struct {
    http.Request
    parsedGet     *gtype.Bool         // GET参数是否已经解析
    parsedPost    *gtype.Bool         // POST参数是否已经解析
    queryVars     map[string][]string // GET参数
    routerVars    map[string][]string // 路由解析参数
    exit          *gtype.Bool         // 是否退出当前请求流程执行
    Id            int                 // 请求id(唯一)
    Server        *Server             // 请求关联的服务器对象
    Cookie        *Cookie             // 与当前请求绑定的Cookie对象(并发安全)
    Session       *Session            // 与当前请求绑定的Session对象(并发安全)
    Response      *Response           // 对应请求的返回数据操作对象
    Router        *Router             // 匹配到的路由对象
    EnterTime     int64               // 请求进入时间(微秒)
    LeaveTime     int64               // 请求完成时间(微秒)
    Param         interface{}         // 开发者自定义参数
    parsedHost    *gtype.String       // 解析过后不带端口号的服务器域名名称
    clientIp      *gtype.String       // 解析过后的客户端IP地址
    isFileRequest bool                // 是否为静态文件请求(非服务请求，当静态文件存在时，优先级会被服务请求高，被识别为文件请求)
}
```
`ghttp.Request`继承了底层的`http.Request`对象，并且包含了会话相关的`Cookie`和`Session`对象(每个请求都会有两个**独立**的`Cookie`和`Session对象`)。此外，每个请求有一个`唯一的Id`（请求Id，全局唯一），用以标识每一个请求。此外，成员对象包含一个与当前请求对应的返回输出对象指针Response，用于数据的返回。

相关方法（API详见： [godoc.org/github.com/johng-cn/gf/g/net/ghttp#Request](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)）：
```go
func (r *Request) AddPost(key string, value string)
func (r *Request) AddQuery(key string, value string)
func (r *Request) AddRouterString(key, value string)
func (r *Request) BasicAuth(user, pass string, tips ...string) bool
func (r *Request) Exit()

func (r *Request) Get(key string, def ...string) string
func (r *Request) GetArray(key string, def ...[]string) []string
func (r *Request) GetClientIp() string
func (r *Request) GetFloat32(key string, def ...float32) float32
func (r *Request) GetFloat64(key string, def ...float64) float64
func (r *Request) GetHost() string
func (r *Request) GetInt(key string, def ...int) int
func (r *Request) GetJson() *gjson.Json
func (r *Request) GetMap(def ...map[string]string) map[string]string
func (r *Request) GetString(key string, def ...string) string
func (r *Request) GetToStruct(object interface{}, mapping ...map[string]string)
func (r *Request) GetUint(key string, def ...uint) uint
func (r *Request) GetRaw() []byte
func (r *Request) GetReferer() string

func (r *Request) GetPost(key string, def ...[]string) []string
func (r *Request) GetPostArray(key string, def ...[]string) []string
func (r *Request) GetPostBool(key string, def ...bool) bool
func (r *Request) GetPostFloat32(key string, def ...float32) float32
func (r *Request) GetPostFloat64(key string, def ...float64) float64
func (r *Request) GetPostInt(key string, def ...int) int
func (r *Request) GetPostMap(def ...map[string]string) map[string]string
func (r *Request) GetPostString(key string, def ...string) string
func (r *Request) GetPostToStruct(object interface{}, mapping ...map[string]string)
func (r *Request) GetPostUint(key string, def ...uint) uint

func (r *Request) GetQuery(key string, def ...[]string) []string
func (r *Request) GetQueryArray(key string, def ...[]string) []string
func (r *Request) GetQueryBool(key string, def ...bool) bool
func (r *Request) GetQueryFloat32(key string, def ...float32) float32
func (r *Request) GetQueryFloat64(key string, def ...float64) float64
func (r *Request) GetQueryInt(key string, def ...int) int
func (r *Request) GetQueryMap(def ...map[string]string) map[string]string
func (r *Request) GetQueryString(key string, def ...string) string
func (r *Request) GetQueryToStruct(object interface{}, mapping ...map[string]string)
func (r *Request) GetQueryUint(key string, def ...uint) uint

func (r *Request) GetRequest(key string, def ...[]string) []string
func (r *Request) GetRequestArray(key string, def ...[]string) []string
func (r *Request) GetRequestBool(key string, def ...bool) bool
func (r *Request) GetRequestFloat32(key string, def ...float32) float32
func (r *Request) GetRequestFloat64(key string, def ...float64) float64
func (r *Request) GetRequestInt(key string, def ...int) int
func (r *Request) GetRequestMap(def ...map[string]string) map[string]string
func (r *Request) GetRequestString(key string, def ...string) string
func (r *Request) GetRequestToStruct(object interface{}, mapping ...map[string]string)
func (r *Request) GetRequestUint(key string, def ...uint) uint

func (r *Request) GetRouterArray(key string) []string
func (r *Request) GetRouterString(key string) string

func (r *Request) IsAjaxRequest() bool
func (r *Request) IsExited() bool
func (r *Request) IsFileRequest() bool
func (r *Request) SetPost(key string, value string)
func (r *Request) SetQuery(key string, value string)
func (r *Request) SetRouterString(key, value string)
func (r *Request) WebSocket() (*WebSocket, error)
```
以上方法可以分为以下几类：
1. ```Get```: 常用方法，简化参数获取，```GetRequestString```的别名；
1. ```GetQuery*```: 获取GET方式传递过来的参数；
2. ```GetPost*```: 获取POST方式传递过来的参数；
3. ```GetRequest*```: 优先查找GET参数中是否有指定键名的参数，如果没有的话返回POST参数中指定键名的参数；
4. ```GetRaw```: 获取原始的客户端提交数据(二进制[ ]byte类型)，与HTTP Method无关(注意由于是读取的请求缓冲区数据，该方法执行一次之后缓冲区便会被清空)；
5. ```GetJson```: 自动将原始请求信息解析为gjson.Json对象返回，gjson.Json对象具体在JSON模块章节中介绍；

其中，获取的参数方法可以对指定键名的数据进行自动类型转换，例如：```http://127.0.0.1:8199/?amount=19.66```，通过```Get```/```GetQueryString```将会返回19.66的字符串类型，```GetQueryFloat32```/```GetQueryFloat64```将会分别返回float32和float64类型的数值19.66。**但是，```GetQueryInt```/```GetQueryUint```将会返回0，而不会返回19或者20，数值的处理交给业务逻辑本身来执行**。
