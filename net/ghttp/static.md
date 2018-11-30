[TOC]

默认情况下，`gf`框架已开启了静态文件服务功能，但是需要开发者配置静态文件目录才能提供服务。

静态文件服务涉及到的常用配置方法如下：
```go
// 设置http server参数 - ServerRoot
func (s *Server) SetServerRoot(root string)

// 添加静态文件搜索目录，必须给定目录的绝对路径
func (s *Server) AddSearchPath(path string)

// 设置http server参数 - IndexFiles，默认展示文件，如：index.html, index.htm
func (s *Server) SetIndexFiles(index []string)

// 是否允许展示访问目录的文件列表
func (s *Server) SetIndexFolder(enabled bool)

// 添加URI与静态目录的映射
func (s *Server) AddStaticPath(prefix string, path string)

// 静态文件服务总开关：是否开启/关闭静态文件服务
func (s *Server) SetFileServerEnabled(enabled bool)
```
其中，
1. `IndexFiles`为当访问目录时默认检索的文件名称，当检索的文件存在时则返回文件内容，否则展示目录列表(`SetIndexFolder`为`true`时)，默认的`IndexFiles`为：index.html, index.htm；
1. `SetIndexFolder`为设置是否在用户访问目录时，且没有检索到`IndexFiles`时，则展示目录下的文件列表，默认为关闭；
1. `SetServerRoot`为设置默认提供服务的静态文件目录，该目录也会自动被添加到`SearchPath`中得第一个搜索路径；
1. `AddSearchPath`为添加当在`ServerRoot`检索不到文件时的附加检索目录路径，可以有多个；
1. `AddStaticPath`为添加URI与目录路径的映射关系，可以自定义静态文件目录的访问URI规则；

# 示例1， 基本使用
```go
package main

import "gitee.com/johng/gf/g"

// 静态文件服务器基本使用
func main() {
    s := g.Server()
    s.SetIndexFolder(true)
    s.SetServerRoot("/Users/john/Temp")
    s.AddSearchPath("/Users/john/Documents")
    s.SetPort(8199)
    s.Run()
}
```

# 示例2，静态目录映射
```go
package main

import "gitee.com/johng/gf/g"

// 静态文件服务器，支持自定义静态目录映射
func main() {
    s := g.Server()
    s.SetIndexFolder(true)
    s.SetServerRoot("/Users/john/Temp")
    s.AddSearchPath("/Users/john/Documents")
    s.AddStaticPath("/my-doc", "/Users/john/Documents")
    s.SetPort(8199)
    s.Run()
}
```

# 示例3，静态目录映射，优先级控制
```go
package main

import "gitee.com/johng/gf/g"

// 静态文件服务器，支持自定义静态目录映射
func main() {
    s := g.Server()
    s.SetIndexFolder(true)
    s.SetServerRoot("/Users/john/Temp")
    s.AddSearchPath("/Users/john/Documents")
    s.AddStaticPath("/my-doc", "/Users/john/Documents")
    s.AddStaticPath("/my-doc/test", "/Users/john/Temp")
    s.SetPort(8199)
    s.Run()
}
```
其中，访问`/my-doc/test`的优先级会被`/my-doc`高，因此假如`/Users/john/Documents`目录下存在`test`目录，将会无法给访问到。

