动态分页是通过```GET```参数(通过```QueryString```)传递分页参数，默认分页参数为```page```。

示例如下：
```go
package main

import (
    "github.com/gogf/gf/g"
    "github.com/gogf/gf/g/os/gview"
    "github.com/gogf/gf/g/net/ghttp"
    "github.com/gogf/gf/g/util/gpage"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/page/demo", func(r *ghttp.Request) {
        page      := gpage.New(100, 10, r.Get("page"), r.URL.String())
        buffer, _ := gview.ParseContent(`
        <html>
            <head>
                <style>
                    a,span {padding:8px; font-size:16px;}
                    div{margin:5px 5px 20px 5px}
                </style>
            </head>
            <body>
                <div>{{.page1}}</div>
                <div>{{.page2}}</div>
                <div>{{.page3}}</div>
                <div>{{.page4}}</div>
            </body>
        </html>
        `, g.Map{
            "page1" : page.GetContent(1),
            "page2" : page.GetContent(2),
            "page3" : page.GetContent(3),
            "page4" : page.GetContent(4),
        })
        r.Response.Write(buffer)
    })
    s.SetPort(8199)
    s.Run()
}
```
该示例中，我们展示了四种预定义的分页样式，并通过GET方式进行分页传参。执行后，输出的内容如下图所示：
![](/images/Selection_009.png)

