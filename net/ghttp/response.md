# 请求输出

数据输出对象定义如下：
```go
// 服务端请求返回对象。
// 注意该对象并没有实现http.ResponseWriter接口，而是依靠ghttp.ResponseWriter实现。
type Response struct {
    ResponseWriter
    length  int             // 请求返回的内容长度(byte)
    Server  *Server         // 所属Web Server
    Writer  *ResponseWriter // ResponseWriter的别名
    request *Request        // 关联的Request请求对象
}
```
可以看到```ghttp.Response```对象继承了标准库的```http.ResponseWriter```对象，因此完全可以使用标准库的方法来进行输出控制。

当然，建议使用```ghttp.Response```封装的方法来控制输出，```ghttp.Response```的数据输出使用```Write*```相关方法实现，并且数据输出采用了Buffer机制，数据的处理效率比较高，任何时候可以通过```OutputBuffer```方法输出缓冲区数据到客户端，并清空缓冲区数据。

相关方法（API详见： [godoc.org/github.com/johng-cn/gf/g/net/ghttp#Response](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)）：
```go
func (r *Response) Buffer() []byte
func (r *Response) BufferLength() int
func (r *Response) ClearBuffer()
func (r *Response) ContentSize() int
func (r *Response) OutputBuffer()
func (r *Response) SetBuffer(buffer []byte)

func (r *Response) RedirectBack()
func (r *Response) RedirectTo(location string)
func (r *Response) ServeFile(path string)
func (r *Response) ServeFileDownload(path string, name ...string)
func (r *Response) SetAllowCrossDomainRequest(allowOrigin string, allowMethods string, maxAge ...int)

func (r *Response) Write(content ...interface{})
func (r *Response) Writef(format string, params ...interface{})
func (r *Response) Writefln(format string, params ...interface{})
func (r *Response) Writeln(content ...interface{})

func (r *Response) WriteXml(content interface{}, rootTag ...string) error
func (r *Response) WriteJson(content interface{}) error
func (r *Response) WriteJsonP(content interface{}) error
func (r *Response) WriteStatus(status int, content ...string)

func (r *Response) WriteTpl(tpl string, params map[string]interface{}, funcmap ...map[string]interface{}) error
func (r *Response) WriteTplContent(content string, params map[string]interface{}, funcmap ...map[string]interface{}) error

func (r *Response) ParseTpl(tpl string, params map[string]interface{}, funcmap ...map[string]interface{}) ([]byte, error)
func (r *Response) ParseTplContent(content string, params map[string]interface{}, funcmap ...map[string]interface{}) ([]byte, error)
```
此外，需要提一下，Header的操作可以通过标准库的方法来实现，例如：
```go
r.Header().Set("Content-Type", "text/plain; charset=utf-8")
```
