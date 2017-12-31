老规矩，我们先来一个Hello World：

    package main

    import "gitee.com/johng/gf/g/net/ghttp"

    func Hello(s *ghttp.Server, r *ghttp.ClientRequest, w *ghttp.ServerResponse) {
        w.WriteString("Hello World!")
    }
    func main() {
        s := ghttp.GetServer()
        s.BindHandler("/", Hello)
        s.Run()
    }
    
这便是一个最简单的Web Server，它不支持静态文件处理，只有一个功能，访问 http://127.0.0.1 的时候，它会返回“Hello World!”。