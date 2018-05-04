

[TOC]

gf框架提供了非常强大的Web Server模块，由```ghttp```包实现，API文档地址： [godoc.org/github.com/johng-cn/gf/g/net/ghttp](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)

>[danger] # 哈喽世界！

老规矩，我们先来一个Hello World：

```go
package main

import "gitee.com/johng/gf/g/net/ghttp"

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/", func(r *ghttp.Request) {
        r.Response.Write("哈喽世界！")
    })
    s.Run()
}
```
这便是一个最简单的Web Server，它不支持静态文件处理，只有一个功能，访问```http://127.0.0.1/```的时候，它会返回“哈喽世界！”。

任何时候，您都可以通过```ghttp.GetServer()```方法获得一个默认的Web Server对象，该方法采用```单例模式```设计，也就是说，多次调用该方法，返回的是同一个Web Server对象（我们也可以通过对象管理器```g.Server()```来获取单例的Web Server对象，后续章节将会有介绍）。

通过Run()方法执行Web Server的监听运行，在没有任何额外设置的情况下，它默认监听80端口。

关于其中的服务注册，我们将会在后续章节中介绍，我们继续来看看如何创建一个支持静态文件的Web Server。


>[danger] # Web Server

创建并运行一个支持静态文件的Web Server：

```go
package main

import "gitee.com/johng/gf/g/net/ghttp"

func main() {
    s := ghttp.GetServer()
    s.SetIndexFolder(true)
    s.SetServerRoot("/home/www/")
    s.Run()
}
```
创建了Web Server对象之后，我们可以使用```Set*```方法来设置Web Server的属性，我们这里的示例中涉及到了两个属性设置方法：
1. ```SetIndexFolder```用来设置是否允许列出Web Server主目录的文件列表（默认为false）；
1. ```SetServerRoot```用来设置Web Server的主目录（默认为空，在某些时候，Web Server仅提供接口服务，因此Web Server的主目录为非必需参数）；

Web Server默认情况下是没有任何主目录的设置，只有设置了主目录，才支持对应主目录下的静态文件的访问。更多属性设置请参考 [ghttp API文档](https://godoc.org/github.com/johng-cn/gf/g/net/ghttp)。

>[danger] # 多服务器支持

ghttp支持多Web Server运行，下面我们来看一个例子：

```go
package main

import (
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s1 := ghttp.GetServer("s1")
    s1.SetAddr(":8080")
    s1.SetIndexFolder(true)
    s1.SetServerRoot("/home/www/static1")
    go s1.Run()

    s2 := ghttp.GetServer("s2")
    s2.SetAddr(":8081")
    s2.SetIndexFolder(true)
    s2.SetServerRoot("/home/www/static2")
    go s2.Run()

    select{}
}
```
如果需要再同一个进程中支持多个Web Server，那么需要将每个Web Server使用goroutine进行异步执行监听，并且通过```select{}```语句(当然您也可以采用其他方式)保持主进程存活。

此外，可以看到我们在支持多个Web Server的语句中，给ghttp.GetServer传递了不同的参数，该参数为Web Server的名称，之前我们提到ghttp的GetServer方法采用了单例设计模式，该参数用于标识不同的Web Server，因此需要保证唯一性。

如果需要获取同一个Web Server，那么传入同一个名称即可。例如在多个goroutine中，或者不同的模块中，都可以通过ghttp.GetServer获取到同一个Web Server对象。

>[danger] # 域名&多域名

**同一个**Web Server支持多域名绑定，并且不同的域名可以绑定不同的服务。

我们来看一个简单的例子：

```go
package main

import "gitee.com/johng/gf/g/net/ghttp"

func Hello1(r *ghttp.Request) {
    r.Response.Write("127.0.0.1: Hello1!")
}

func Hello2(r *ghttp.Request) {
    r.Response.Write("localhost: Hello2!")
}

func main() {
    s := ghttp.GetServer()
    s.Domain("127.0.0.1").BindHandler("/", Hello1)
    s.Domain("localhost").BindHandler("/", Hello2)
    s.Run()
}
```
我们访问```http://127.0.0.1/```和```http://localhost/```可以看输出不同的内容。

此外，```Domain```方法支持多个域名参数，使用英文“,”号分隔，例如：

	s.Domain("localhost1,localhost2,localhost3").BindHandler("/", Hello2)
    
这条语句的表示将Hello2方法注册到指定的3个域名中(localhost1~3)，对其他域名不可见。

需要注意的是：Domain方法的参数必须是准确的域名，**不支持泛域名形式**，例如：*.johng.cn或者.johng.cn是不支持的，api.johng.cn或者johng.cn才被认为是正确的域名参数。



>[danger] # 多端口监听

ghttp.Server同时支持多端口监听，只需要往```SetPort```参数设置绑定多个端口号即可（当然，针对于HTTPS服务，我们也同样可以通过```SetHTTPSPort```来设置绑定并支持多个端口号的监听，HTTPS服务的介绍请参看后续对应章节）。

我们来看一个例子：

```go
package main

import (
    "gitee.com/johng/gf/g/net/ghttp"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/", func(r *ghttp.Request){
        r.Response.Writeln("go frame!")
    })
    s.SetPort(8100, 8200, 8300)
    s.Run()
}
```

执行以上示例后，我们访问以下URL将会得到期望的相同结果：
```shell
http://127.0.0.1:8100/
http://127.0.0.1:8200/
http://127.0.0.1:8300/
```


>[danger] # HTTPS服务


ghttp.Server支持HTTPS服务，HTTPS的详细介绍请参考【[HTTPS服务](HTTPS服务.md)】章节。