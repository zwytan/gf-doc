[TOC]


# 分组路由

`gf`框架当然也支持分组路由的注册方式，可以给分组路由指定一个`prefix`前缀（可选），在该分组下的所有路由注册都将注册在该路由前缀下。

方法列表：

```go
func (s *Server) Group(prefix...string) *RouterGroup
func (d *Domain) Group(prefix...string) *RouterGroup

func (g *RouterGroup) ALL(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) GET(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) PUT(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) POST(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) DELETE(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) PATCH(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) HEAD(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) CONNECT(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) OPTIONS(pattern string, object interface{}, params...interface{})
func (g *RouterGroup) TRACE(pattern string, object interface{}, params...interface{})

// REST路由注册
func (g *RouterGroup) REST(pattern string, object interface{})

// 执行分组路由批量绑定
func (g *RouterGroup) Bind(group string, items []GroupItem)
```

# 基本使用
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/frame/gmvc"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Writeln("Object Show")
}

func (o *Object) Delete(r *ghttp.Request) {
    r.Response.Writeln("Object REST Delete")
}

type Controller struct {
    gmvc.Controller
}

func (c *Controller) Show() {
    c.Response.Writeln("Controller Show")
}

func (c *Controller) Post() {
    c.Response.Writeln("Controller REST Post")
}

func Handler(r *ghttp.Request) {
    r.Response.Writeln("Handler")
}

func HookHandler(r *ghttp.Request) {
    r.Response.Writeln("Hook Handler")
}

func main() {
    s   := g.Server()
    g   := s.Group("/api")
    obj := new(Object)
    ctl := new(Controller)
    g.ALL ("*",            HookHandler, ghttp.HOOK_BEFORE_SERVE)
    g.ALL ("/handler",     Handler)
    g.ALL ("/ctl",         ctl)
    g.GET ("/ctl/my-show", ctl, "Show")
    g.REST("/ctl/rest",    ctl)
    g.ALL ("/obj",         obj)
    g.GET ("/obj/my-show", obj, "Show")
    g.REST("/obj/rest",    obj)
    s.SetPort(8199)
    s.Run()
}
```
执行后，注册的路由表如下：
```
  SERVER  | ADDRESS | DOMAIN  | METHOD | P |      ROUTE       |         HANDLER         |    HOOK      
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 2 | /api/*           | main.HookHandler        | BeforeServe  
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | GET    | 3 | /api/ctl/my-show | main.(*Controller).Show |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 3 | /api/ctl/post    | main.(*Controller).Post |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 3 | /api/ctl/show    | main.(*Controller).Show |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | POST   | 3 | /api/ctl/rest    | main.(*Controller).Post |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | GET    | 3 | /api/obj/my-show | main.(*Object).Show     |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 3 | /api/obj/delete  | main.(*Object).Delete   |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | DELETE | 3 | /api/obj/rest    | main.(*Object).Delete   |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 3 | /api/obj/show    | main.(*Object).Show     |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
  default | :8199   | default | ALL    | 2 | /api/handler     | main.Handler            |              
|---------|---------|---------|--------|---|------------------|-------------------------|-------------|
```

# 批量注册

`gf`框架的分组路由同时也支持批量的路由注册方式。

```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/frame/gmvc"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Writeln("Object Show")
}

func (o *Object) Delete(r *ghttp.Request) {
    r.Response.Writeln("Object REST Delete")
}

type Controller struct {
    gmvc.Controller
}

func (c *Controller) Show() {
    c.Response.Writeln("Controller Show")
}

func (c *Controller) Post() {
    c.Response.Writeln("Controller REST Post")
}

func Handler(r *ghttp.Request) {
    r.Response.Writeln("Handler")
}

func HookHandler(r *ghttp.Request) {
    r.Response.Writeln("Hook Handler")
}

func main() {
    s   := g.Server()
    obj := new(Object)
    ctl := new(Controller)
    s.Group("/api").Bind("/api", []ghttp.GroupItem{
        {"ALL",  "*",            HookHandler, ghttp.HOOK_BEFORE_SERVE},
        {"ALL",  "/handler",     Handler},
        {"ALL",  "/ctl",         ctl},
        {"GET",  "/ctl/my-show", ctl, "Show"},
        {"REST", "/ctl/rest",    ctl},
        {"ALL",  "/obj",         obj},
        {"GET",  "/obj/my-show", obj, "Show"},
        {"REST", "/obj/rest",    obj},
    })
    s.SetPort(8199)
    s.Run()
}
```
执行后，注册的路由表和前一个示例一致。














