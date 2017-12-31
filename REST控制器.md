REST控制器即是采用RESTful设计方式的控制器，通常用于API服务。

在这种模式下，HTTP的Method将会映射到控制器对应的方法，例如：POST方式将会映射到控制器的Post方法中，DELETE方式将会映射到控制器的Delete方法中。
其他非HTTP Method命名的方法，即使是公开方法，将无法完成自动注册，对于应用端不可见。
这种方式注册的控制器，运行模式和“控制器注册”模式相同。

我们可以通过BindControllerRest完成RESTful控制器的注册。
以下是一个示例：
```go
package demo

import (
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/frame/gmvc"
)

// 测试控制器
type ControllerRest struct {
    gmvc.Controller
}

// 初始化控制器对象，并绑定操作到Web Server
func init() {
    // 控制器公开方法中与HTTP Method方法同名的方法将会自动绑定映射
    ghttp.GetServer().BindControllerRest("/user", &ControllerRest{})
}

// RESTFul - GET
func (c *ControllerUser) Get() {
    c.Response.WriteString("RESTFul HTTP Method GET")
}

// RESTFul - POST
func (c *ControllerUser) Post() {
    c.Response.WriteString("RESTFul HTTP Method POST")
}

// RESTFul - DELETE
func (c *ControllerUser) Delete() {
    c.Response.WriteString("RESTFul HTTP Method DELETE")
}

// 该方法无法映射，将会无法访问到
func (c *ControllerUser) Hello() {
    c.Response.WriteString("Hello")
}
```




