>[danger] # 请求输入

请求输入依靠 ghttp.Request 对象实现：
```go
type Request struct {
    http.Request
    parsedGet  *gtype.Bool         // GET参数是否已经解析
    parsedPost *gtype.Bool         // POST参数是否已经解析
    values     map[string][]string // GET参数
    exit       *gtype.Bool         // 是否退出当前请求流程执行
    Id         int                 // 请求id(唯一)
    Server     *Server             // 请求关联的服务器对象
    Cookie     *Cookie             // 与当前请求绑定的Cookie对象(并发安全)
    Session    *Session            // 与当前请求绑定的Session对象(并发安全)
    Response   *Response           // 对应请求的返回数据操作对象
    Router     *Router             // 匹配到的路由对象
    EnterTime  int64               // 请求进入时间(微秒)
    LeaveTime  int64               // 请求完成时间(微秒)
    Param      interface{}         // 开发者自定义参数
}
```
```ghttp.Request```继承了底层的http.Request对象，并且包含了会话相关的Cookie和Session对象(每个请求都会有两个独立的Cookie和Session对象)。此外，每个请求有一个唯一的Id（请求Id，全局唯一），用以标识每一个请求。此外，成员对象包含一个与当前请求对应的返回输出对象指针Response，用于数据的返回。

相关方法（API详见： [godoc.org/github.com/johng-cn/gf/g/net/ghttp#Request](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)）：
```go
func (r *Request) Get(k string) string

func (r *Request) GetJson() *gjson.Json
func (r *Request) GetPost(k string) []string
func (r *Request) GetPostArray(k string) []string
func (r *Request) GetPostBool(k string) bool
func (r *Request) GetPostFloat32(k string) float32
func (r *Request) GetPostFloat64(k string) float64
func (r *Request) GetPostInt(k string) int
func (r *Request) GetPostMap(defaultMap map[string]string) map[string]string
func (r *Request) GetPostString(k string) string
func (r *Request) GetPostUint(k string) uint
func (r *Request) GetQuery(k string) []string
func (r *Request) GetQueryArray(k string) []string
func (r *Request) GetQueryBool(k string) bool
func (r *Request) GetQueryFloat32(k string) float32
func (r *Request) GetQueryFloat64(k string) float64
func (r *Request) GetQueryInt(k string) int
func (r *Request) GetQueryMap(defaultMap map[string]string) map[string]string
func (r *Request) GetQueryString(k string) string
func (r *Request) GetQueryUint(k string) uint
func (r *Request) GetRaw() []byte
func (r *Request) GetRequest(k string) []string
func (r *Request) GetRequestArray(k string) []string
func (r *Request) GetRequestBool(k string) bool
func (r *Request) GetRequestFloat32(k string) float32
func (r *Request) GetRequestFloat64(k string) float64
func (r *Request) GetRequestInt(k string) int
func (r *Request) GetRequestMap(defaultMap map[string]string) map[string]string
func (r *Request) GetRequestString(k string) string
func (r *Request) GetRequestUint(k string) uint

func (r *Request) Exit()
func (r *Request) IsExited() bool
```
以上方法可以分为以下几类：
1. ```Get```: 常用方法，简化参数获取，```GetRequestString```的别名；
1. ```GetQuery*```: 获取GET方式传递过来的参数；
2. ```GetPost*```: 获取POST方式传递过来的参数；
3. ```GetRequest*```: 优先查找GET参数中是否有指定键名的参数，如果没有的话返回POST参数中指定键名的参数；
4. ```GetRaw```: 获取原始的客户端提交数据(二进制[ ]byte类型)，与HTTP Method无关(注意由于是读取的请求缓冲区数据，该方法执行一次之后缓冲区便会被清空)；
5. ```GetJson```: 自动将原始请求信息解析为gjson.Json对象返回，gjson.Json对象具体在JSON模块章节中介绍；

其中，获取的参数方法可以对指定键名的数据进行自动类型转换，例如：```http://127.0.0.1:8199/?amount=19.66```，通过```Get```/```GetQueryString```将会返回19.66的字符串类型，```GetQueryFloat32```/```GetQueryFloat64```将会分别返回float32和float64类型的数值19.66。**但是，```GetQueryInt```/```GetQueryUint```将会返回0，而不会返回19或者20，数值的处理交给业务逻辑本身来执行**。

<!--
>[danger] # 流程自定义传参

ghttp.Request对象有一个```Param```参数属性，该属性用于开发者请求流程处理的自定义传参参数，它的数据类型为```interface{}```，因此可以传递任何类型的参数。我们使用一个例子来说明如何使用ghttp.Request的自定义传参特性：



