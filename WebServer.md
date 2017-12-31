创建并运行一个支持静态文件的Web Server：

    package main

    import "gitee.com/johng/gf/g/net/ghttp"

    func main() {
        s := ghttp.GetServer()
        s.SetIndexFolder(true)
        s.SetServerRoot("/home/www/static")
        s.Run()
    }

创建了Web Server对象之后，我们可以使用Set*方法来设置Web Server的属性。
1. SetIndexFolder 用来设置是否允许列出Web Server主目录的文件列表（默认为false）；
1. SetServerRoot 用来设置Web Server的主目录（默认为空，在某些时候，Web Server仅提供接口服务，因此Web Server的主目录为非必需参数）；

Web Server默认情况下是没有任何主目录的设置，只有设置了主目录，才支持对应主目录下的静态文件的访问。
