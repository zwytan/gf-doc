和REST控制器注册类似，只不过注册的是一个实例化的对象。

我们可以通过**ghttp.BindObjectRest**方法完成REST对象的注册。

以下是一个示例(gitee.com/johng/gf/geg/frame/mvc/controller/demo/object_rest.go)：

```go
package demo

import "gitee.com/johng/gf/g/net/ghttp"

type ObjectRest struct {}

func init() {
    ghttp.GetServer().BindObjectRest("/object-rest", &ObjectRest{})
}

func (o *ObjectRest) Get(s *ghttp.Server, r *ghttp.ClientRequest, w *ghttp.ServerResponse) {
    w.WriteString("It's show time bibi!")
}
```