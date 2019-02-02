
[TOC]

# 使用方法

## 通用视图管理

通用视图管理即使用原生的模板引擎```gview```模块来实现模板管理，包括模板读取展示，模板变量渲染等等，可以通过对象管理器```g.View()```来获取默认的单例`gview`对象。

接口文档：
[godoc.org/github.com/gogf/gf/g/os/gview](https://godoc.org/github.com/gogf/gf/g/os/gview)
```go
func HTML(content string) template.HTML
func ParseContent(content string, params map[string]interface{}) ([]byte, error)

type View
    func Get(path string) *View
    func New(path string) *View
    
    func (view *View) AddPath(path string) error
    func (view *View) SetPath(path string) error

    func (view *View) Assign(key string, value interface{})
    func (view *View) Assigns(data Params)

    func (view *View) BindFunc(name string, function interface{})
    func (view *View) Parse(file string, params map[string]interface{}, funcmap ...map[string]interface{}) ([]byte, error)
    func (view *View) ParseContent(content string, params map[string]interface{}, funcmap ...map[string]interface{}) ([]byte, error)
    func (view *View) SetDelimiters(left, right string)
```
简要说明：
1. `gview.Get`用于根据给定的一个模板目录路径，获得对应的单例模板引擎对象；
1. `gview.New`同样可以根据给定的模板目录路径创建模板引擎对象，但没有单例管理；
1. `SetPath/AddPath`用于设置/添加当前模板引擎对象的模板目录路径，其中`SetPath`会覆盖所有的模板目录设置，推荐使用`AddPath`；
1. `Assign/Assigns`用于设置模板变量，通过该模板引擎解析的所有模板均可以使用这些模板变量；
1. `BindFunc`用于绑定模板函数，具体使用方法参考后续示例程序；
1. `Parse/ParseContent`用于解析模板文件/内容，可以在解析时给定临时的模板变量及模板函数；
1. `SetDelimiters`用于设置该模板引擎对象的模板解析分隔符号，默认为`{{ }}`（与`vuejs`前端框架有冲突）；



### 示例1，解析模板文件
```go
package main

import (
    "github.com/gogf/gf/g/net/ghttp"
    "github.com/gogf/gf/g/frame/gins"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/template2", func(r *ghttp.Request){
        content, _ := gins.View().Parse("index.tpl", map[string]interface{}{
            "id"   : 123,
            "name" : "john",
        })
        r.Response.Write(content)
    })
    s.SetPort(8199)
    s.Run()
}
```
在这个示例中我们使用单例管理器获取一个默认的视图对象，随后通过该视图渲染对应模板目录下的```index.tpl```模板文件并给定模板变量参数。

我们也可以通过```SetPath/Addpath```方法中心指定视图对象的模板目录，该方法是并发安全的，但是需要注意一旦改变了该视图对象的模板目录，将会在整个进程中生效。当然，也可以直接解析模板内容。

### 示例2，解析模板内容
```go
package main

import (
    "github.com/gogf/gf/g/net/ghttp"
    "github.com/gogf/gf/g/frame/gins"
)

func main() {
    s := ghttp.GetServer()
    s.BindHandler("/template2", func(r *ghttp.Request){
        tplcontent := `id:{{.id}}, name:{{.name}}`
        content, _ := gins.View().ParseContent(tplcontent, map[string]interface{}{
            "id"   : 123,
            "name" : "john",
        })
        r.Response.Write(content)
    })
    s.SetPort(8199)
    s.Run()
}
```
执行后，访问```http://127.0.0.1:8199/template2```可以看到解析后的内容为：```id:123, name:john```

### 示例3，自定义模板变量分隔符

在项目中我们经常会遇到Golang默认模板变量分隔符号与```Vue```的变量分隔符号冲突的情况（都使用的是```{{ }}```），我们可以使用```SetDelimiters```方法来自定义全局的Golang模板变量分隔符号：
```go
// main.go
package main

import (
    "fmt"
    "github.com/gogf/gf/g"
)

func main() {
    v := g.View()
    v.SetDelimiters("${", "}")
    b, err := v.Parse("gview_delimiters.tpl", map[string]interface{} {
        "k" : "v",
    })
    fmt.Println(err)
    fmt.Println(string(b))
}
```
```html
<!-- gview_delimiters.tpl -->
test.tpl content, vars: ${.}
```
执行后，输出结果为：
```shell
<nil>
test.tpl content, vars: map[k:v]
```



## 控制器视图管理

gf为控制器提供了良好的模板引擎支持，由```gmvc.View```视图对象进行管理，提供了良好的数据`隔离性`。控制器视图是并发安全设计的，允许在多线程中异步操作。
```go
func (view *View) Assign(key string, value interface{})
func (view *View) Assigns(data gview.Params)

func (view *View) Parse(file string) ([]byte, error)
func (view *View) ParseContent(content string) ([]byte, error)

func (view *View) Display(files ...string) error
func (view *View) DisplayContent(content string) error

func (view *View) LockFunc(f func(vars map[string]interface{}))
func (view *View) RLockFunc(f func(vars map[string]interface{}))
```

使用示例1：
```go
package main

import (
    "github.com/gogf/gf/g/net/ghttp"
    "github.com/gogf/gf/g/frame/gmvc"
)

type ControllerTemplate struct {
    gmvc.Controller
}

func (c *ControllerTemplate) Info() {
    c.View.Assign("name", "john")
    c.View.Assigns(map[string]interface{}{
        "age"   : 18,
        "score" : 100,
    })
    c.View.Display("index.tpl")
}

func main() {
    s := ghttp.GetServer()
    s.BindController("/template", new(ControllerTemplate))
    s.SetPort(8199)
    s.Run()
}
```
其中```index.tpl```的模板内容如下：
```html
<html>
    <head>
        <title>gf template engine</title>
    </head>
    <body>
        <p>Name: {{.name}}</p>
        <p>Age:  {{.age}}</p>
        <p>Score:{{.score}}</p>
    </body>
</html>
```
执行后，访问```http://127.0.0.1:8199/template/info```可以看到模板被解析并展示到页面上。如果页面报错找不到模板文件，没有关系，因为这里并没有对模板目录做设置，默认是当前可行文件的执行目录（Linux&Mac下是```/tmp```目录，Windows下是```C:\Documents and Settings\用户名\Local Settings\Temp ```）。如何手动设置模板文件目录请查看后续章节，随后可回过头来手动修改目录后看到结果。

其中，给定的模板文件file参数是需要带完整的文件名后缀，例如：```index.tpl```，```index.html```等等，模板引擎对模板文件后缀名没有要求，用户可完全自定义。此外，模板文件参数也支持文件的绝对路径(完整的文件路径)。

当然，我们也可以直接解析模板内容，请看示例2：
```go
package main

import (
    "github.com/gogf/gf/g/net/ghttp"
    "github.com/gogf/gf/g/frame/gmvc"
)

type ControllerTemplate struct {
    gmvc.Controller
}

func (c *ControllerTemplate) Info() {
    c.View.Assign("name", "john")
    c.View.Assigns(map[string]interface{}{
        "age"   : 18,
        "score" : 100,
    })
    c.View.DisplayContent(`
        <html>
            <head>
                <title>gf template engine</title>
            </head>
            <body>
                <p>Name: {{.name}}</p>
                <p>Age:  {{.age}}</p>
                <p>Score:{{.score}}</p>
            </body>
        </html>
    `)
}

func main() {
    s := ghttp.GetServer()
    s.BindController("/template", new(ControllerTemplate{}))
    s.SetPort(8199)
    s.Run()
}
```
执行后，访问```http://127.0.0.1:8199/template/info```可以看到解析后的内容如下：
```html
<html>
    <head>
        <title>gf template engine</title>
    </head>
    <body>
        <p>Name: john</p>
        <p>Age:  18</p>
        <p>Score:100</p>
    </body>
</html>
```





## 修改模板目录

`gf`框架的模板引擎支持两种方式的模板目录修改。

## 修改模板目录
模板引擎作为`gf`框架的核心组件，可以通过以下方式修改模板引擎的默认模板文件查找目录：
1. (推荐)单例模式获取全局View对象，通过```SetPath```方法手动修改；
2. 修改命令行启动参数 - ```gf.gview.path```；
3. 修改指定的环境变量 - ```GF_GVIEW_PATH```；

例如，我们的执行程序文件为`main`，那么可以通过以下方式修改模板引擎的模板目录(Linux下)：

1. (推荐)通过单例模式
	```go
    gins.View().SetPath("/opt/template")
    ```
3. 通过命令行参数
    ```shell
    ./main --gf.gview.path=/opt/template/
    ```
1. 通过环境变量
    * 启动时修改环境变量：
        ```shell
        GF_GVIEW_PATH=/opt/config/; ./main
        ```
    * 使用`genv`模块来修改环境变量：
        ```go
        genv.Set("GF_GVIEW_PATH", "/opt/template")
        ```

## 添加搜索目录

gf框架的模板引擎支持非常灵活的多目录自动搜索功能，通过```SetPath```可以修改模板目录为唯一的一个目录地址，同时，我们可以通过```AddPath```方法添加多个搜索目录(推荐)，模板引擎底层将会按照添加目录的顺序作为优先级进行自动检索。直到检索到一个匹配的文件路径为止，如果在所有搜索目录下查找不到模板文件，那么会返回失败。

当我们使用对象管理器```g.View()```获取模板引擎单例对象时，框架会自动添加两个模板引擎搜索目录：
* 当前可执行文件的目录；
* 源代码`main包`文件目录(仅对源码开发环境有效)；

## 自动检测更新

模板引擎使用了缓存机制，当模板文件第一次被读取后会被缓存到内存，下一次读取时将会直接从缓存中获取，以提高执行效率。并且，模板引擎提供了对模板文件的**自动检测更新机制**，当模板文件在外部被修改后，模板引擎能够即时地监控到并刷新模板文件的缓存内容。

模板引擎的自动检测更新机制是`gf`框架特有的一大特色。

