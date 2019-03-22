[TOC]


# HTTP客户端

## ghttp.Client

`gf`框架提供了强大易用的HTTP客户端，由`ghttp`实现(ghttp包统一封装了HTTP客户端及服务端功能)，我们先来看一下HTTP客户端的方法有哪些：
https://godoc.org/github.com/gogf/gf/g/net/ghttp#Client
```go
type Client
    func NewClient() *Client
    func (c *Client) Get(url string) (*ClientResponse, error)
    func (c *Client) Put(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Post(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Delete(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Connect(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Head(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Options(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Patch(url string, data ...string) (*ClientResponse, error)
    func (c *Client) Trace(url string, data ...string) (*ClientResponse, error)
    func (c *Client) DoRequest(method, url string, data ...string) (*ClientResponse, error)

    func (c *Client) GetContent(url string, data ...string) string
    func (c *Client) PutContent(url string, data ...string) string
    func (c *Client) PostContent(url string, data ...string) string
    func (c *Client) DeleteContent(url string, data ...string) string
    func (c *Client) ConnectContent(url string, data ...string) string
    func (c *Client) HeadContent(url string, data ...string) string
    func (c *Client) OptionsContent(url string, data ...string) string
    func (c *Client) PatchContent(url string, data ...string) string
    func (c *Client) TraceContent(url string, data ...string) string
    func (c *Client) DoRequestContent(method string, url string, data ...string) string

    func (c *Client) SetBasicAuth(user, pass string)
    func (c *Client) SetBrowserMode(enabled bool)
    func (c *Client) SetCookie(key, value string)
    func (c *Client) SetCookieMap(cookieMap map[string]string)
    func (c *Client) SetHeader(key, value string)
    func (c *Client) SetHeaderRaw(header string)
    func (c *Client) SetPrefix(prefix string)
    func (c *Client) SetTimeOut(t time.Duration)
```
我们可以使用```ghttp.NewClient```创建一个HTTP客户端对象，随后可以使用该对象执行请求。`ghttp.Clien`t对象中封装了一系列基于`HTTP Method`来命名的方法，调用这些方法将会发起对应的`HTTP Method`请求。常用的方法当然是`Get`和`Post`方法，此外`DoRequest`是核心的请求方法，用户可以调用该方法实现自定义的`HTTP Method`发送请求。

`ghttp`模块也提供了独立的包函数来实现HTTP请求，函数列表如下：

```go
func Get(url string) (*ClientResponse, error)
func Put(url string, data...string) (*ClientResponse, error)
func Post(url string, data...string) (*ClientResponse, error)
func Delete(url string, data...string) (*ClientResponse, error)
func Head(url string, data...string) (*ClientResponse, error)
func Patch(url string, data...string) (*ClientResponse, error)
func Connect(url string, data...string) (*ClientResponse, error)
func Options(url string, data...string) (*ClientResponse, error)
func Trace(url string, data...string) (*ClientResponse, error)
func DoRequest(method, url string, data...string) (*ClientResponse, error)

func GetContent(url string, data...string) string
func PutContent(url string, data...string) string
func PostContent(url string, data...string) string
func DeleteContent(url string, data...string) string
func HeadContent(url string, data...string) string
func PatchContent(url string, data...string) string
func ConnectContent(url string, data...string) string 
func OptionsContent(url string, data...string) string
func TraceContent(url string, data...string) string 
func RequestContent(method string, url string, data...string) string 
```
函数说明与`Client`对象的方法说明一致，因此比较常见的情况是直接使用`ghttp`对应的HTTP包方法来实现HTTP客户端请求，而不用创建一个`Client`对象来处理。不过，当需要自定义请求的一些细节(例如超时时间、Cookie、Header等)时，就得依靠自定义的Client对象来处理了(需要`New`一个`ghttp.Client`对象来控制)。

## ghttp.ClientResponse

与`ghttp.Client`对应的是```ghttp.ClientResponse```，表示HTTP对应请求的返回结果对象，该对象继承于`http.Response`，可以使用`http.Response`的所有方法。在此基础之上增加了以下两个方法：
```go
func (r *ClientResponse) GetCookie(key string) string
func (r *ClientResponse) ReadAll() []byte
func (r *ClientResponse) ReadAllString()
func (r *ClientResponse) Close()
```
这里也要提醒的是，**ghttp.ClientResponse是需要手动调用`Close`方法关闭的**，也就是说，不管你使用不使用返回的```ghttp.ClientResponse```对象，你都需要将该返回对象赋值给一个变量，并且（在不使用的时候）手动调用其```Close```方法进行关闭。需要手动关闭返回对象这一点，与标准库的HTTP客户端请求对象操作相同。

## 数组及复杂类型参数

数组参数可以通过例如`array=1&array=2&array=3`这样的方式传递，但是推荐复杂数据类型使用`JSON`数据格式传递。

> 由于`gf`框架的`WebServer`基于`net/http`标准库，因此遵循标准库的参数设计，`array=1&array=2&array=3`这样的提交参数将会被解析为数组变量`array`，不支持`array[]=1&array[]=2&array[]=3`这样的数组提交方式，也不支持`map[a]=1&map[b]=2&map[c]=3`这样的`map`提交方式。

## 基本示例

我们来看几个HTTP客户端请求的例子：

1. **发送GET请求，并打印出返回值**
    ```go
    if r, e := ghttp.Get("https://johng.cn"); e != nil {
        fmt.Println(e)
    } else {
        fmt.Println(string(r.ReadAll()))
        r.Close()
    }
    ```
1. **发送POST请求，并打印出返回值**
    ```go
    if r, e := ghttp.Post("http://127.0.0.1:8199/form", "name=john&age=18"); e != nil {
        fmt.Println(e)
    } else {
        fmt.Println(string(r.ReadAll()))
        r.Close()
    }
    ```

1. **发送POST请求，编码请求参数，并打印出返回值**
    ```go
    params := ghttp.BuildParams(g.Map{
        "submit"   : "1",
        "callback" : "http://127.0.0.1/callback?url=http://baidu.com",
    })
    if r, e := ghttp.Post("http://127.0.0.1:8199/form", params); e != nil {
        fmt.Println(e)
    } else {
        fmt.Println(string(r.ReadAll()))
        r.Close()
    }
    ```
    对于复杂参数的请求处理来讲，这是一个非常典型的例子。gf的HTTP客户端设计得非常简洁，请求参数类型被设计为最常用的string类型，因此在传递多参数的时候用户可以使用"&"符号进行连接，但当参数中带有特殊字符时，需要使用```ghttp.BuildParams```方法进行编码处理，并生成用于请求的参数字符串。
    当然，您也可以使用```gurl.Encode```方法来自行对参数进行编码处理。

1. **发送DELETE请求，并打印出返回值**
    ```go
    if r, e := ghttp.Delete("http://127.0.0.1:8199/user", "10000"); e != nil {
        fmt.Println(e)
    } else {
        fmt.Println(string(r.ReadAll()))
        r.Close()
    }
    ```


## 文件上传

`gf`的HTTP客户端封装并极大简化了文件上传功能，直接上例子：

1. **客户端**
    [github.com/gogf/gf/blob/master/geg/net/ghttp/client/upload/client.go](https://github.com/gogf/gf/blob/master/geg/net/ghttp/client/upload/client.go)

    ```go
    package main

    import (
        "fmt"
        "github.com/gogf/gf/g/os/glog"
        "github.com/gogf/gf/g/net/ghttp"
    )

    func main() {
        path := "/home/john/Workspace/Go/github.com/gogf/gf/version.go"
        r, e := ghttp.Post("http://127.0.0.1:8199/upload", "name=john&age=18&upload-file=@file:" + path)
        if e != nil {
            glog.Error(e)
        } else {
            fmt.Println(string(r.ReadAll()))
            r.Close()
        }
    }
    ```

    可以看到，文件上传参数格式使用 `参数名=@file:文件路径` ，HTTP客户端将会自动解析**文件路径**对应的文件内容并读取提交给服务端。原本复杂的文件上传操作被gf进行了封装处理，用户只需要使用 ```@file:+文件路径``` 来构成参数值即可。
1. **服务端**
    [github.com/gogf/gf/blob/master/geg/net/ghttp/client/upload/server.go](https://github.com/gogf/gf/blob/master/geg/net/ghttp/client/upload/server.go)

    ```go
    package main

    import (
        "github.com/gogf/gf/g"
        "github.com/gogf/gf/g/os/gfile"
        "github.com/gogf/gf/g/net/ghttp"
    )

    // 执行文件上传处理，上传到系统临时目录 /tmp
    func Upload(r *ghttp.Request) {
        if f, h, e := r.FormFile("upload-file"); e == nil {
            defer f.Close()
            name   := gfile.Basename(h.Filename)
            buffer := make([]byte, h.Size)
            f.Read(buffer)
            gfile.PutBinContents("/tmp/" + name, buffer)
            r.Response.Write(name + " uploaded successly")
        } else {
            r.Response.Write(e.Error())
        }
    }

    // 展示文件上传页面
    func UploadShow(r *ghttp.Request) {
        r.Response.Write(`
        <html>
        <head>
            <title>上传文件</title>
        </head>
            <body>
                <form enctype="multipart/form-data" action="/upload" method="post">
                    <input type="file" name="upload-file" />
                    <input type="submit" value="upload" />
                </form>
            </body>
        </html>
        `)
    }

    func main() {
        s := g.Server()
        s.BindHandler("/upload",      Upload)
        s.BindHandler("/upload/show", UploadShow)
        s.SetPort(8199)
        s.Run()
    }
    ```
    访问 ```http://127.0.0.1:8199/upload/show``` 选择需要上传的文件，提交之后可以看到文件上传成功到服务器上。

    文件上传比较简单，**但是需要注意的是，服务端在上传处理中需要使用f.Close() 关闭掉临时上传文件指针**。

## 自定义Header

http客户端发起请求时可以自定义发送给服务端的Header内容，该特性使用```SetHeader(key, value string)```方法实现。我们来看一个客户端自定义Cookie的例子。

1. **客户端**
    
    [github.com/gogf/gf/blob/master/geg/net/ghttp/client/cookie/client.go](https://github.com/gogf/gf/blob/master/geg/net/ghttp/client/cookie/client.go)
    ```go
    package main

    import (
        "fmt"
        "github.com/gogf/gf/g/os/glog"
        "github.com/gogf/gf/g/net/ghttp"
    )

    func main() {
        c := ghttp.NewClient()
        c.SetHeader("Cookie", "name=john; score=100")
        if r, e := c.Get("http://127.0.0.1:8199/"); e != nil {
            glog.Error(e)
        } else {
            fmt.Println(string(r.ReadAll()))
        }
    }
    ```
    通过```ghttp.NewClient()```创建一个自定义的http请求客户端对象，并通过```c.SetHeader("Cookie", "name=john; score=100")```设置自定义的Cookie，这里我们设置了两个示例用的Cookie参数，一个```name```，一个```score```，注意多个Cookie参数使用```;```符号分隔。
1. **服务端**
    
    [github.com/gogf/gf/blob/master/geg/net/ghttp/client/cookie/server.go](https://github.com/gogf/gf/blob/master/geg/net/ghttp/client/cookie/server.go)
    ```go
    package main

    import (
        "github.com/gogf/gf/g"
        "github.com/gogf/gf/g/net/ghttp"
    )

    func main() {
        s := g.Server()
        s.BindHandler("/", func(r *ghttp.Request){
            r.Response.Writeln(r.Cookie.Map())
        })
        s.SetPort(8199)
        s.Run()
    }
    ```
	服务端的逻辑很简单，直接将接收到的Cookie参数全部打印出来。

1. **执行结果**

	客户端代码执行后，终端将会打印出服务端的返回结果，如下：
    ```shell
    map[name:john score:100 gfsessionid:1ADDSEU5QN425798]
    ```
    可以看到，服务端已经接收到了客户端自定义的Cookie参数。其中```gfsessionid```是每个http请求都会有的`sessionid`，是gf框架自带的Cookie参数。
