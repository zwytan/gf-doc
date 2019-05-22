# 链式操作

`glog`模块支持非常简便的`链式操作`方式，相关链式操作方法如下：
```go
// 重定向日志输出接口
func To(writer io.Writer) *Logger
// 日志内容输出到目录
func Path(path string) *Logger
// 设置日志文件分类
func Cat(category string) *Logger
// 设置日志文件格式
func File(file string) *Logger
// 设置日志打印级别
func Level(level int) *Logger
// 是否开启trace打印
func Backtrace(enabled bool) *Logger
// 是否开启终端输出
func Stdout(enabled...bool) *Logger
// 是否输出日志头信息
func Header(enabled...bool) *Logger
// 输出日志调用文件信息
func Line(long...bool) *Logger
```

## 示例1, 基本使用
```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gfile"
    "github.com/gogf/gf/g"
)

func main() {
    path := "/tmp/glog-cat"
    glog.SetPath(path)
    glog.Stdout(false).Cat("cat1").Cat("cat2").Println("test")
    list, err := gfile.ScanDir(path, "*", true)
    g.Dump(err)
    g.Dump(list)
}
```
执行后，输出结果为：
```html
null
[
	"/tmp/glog-cat/cat1",
	"/tmp/glog-cat/cat1/cat2",
	"/tmp/glog-cat/cat1/cat2/2018-10-10.log",
]
```

## 示例2, 打印调用文件

```go
package main

import (
	"github.com/gogf/gf/g/os/glog"
)

func main() {
	glog.Line().Println("123")
	glog.Line(true).Println("123")
}
```
执行后，终端输出结果为：
```html
2019-05-22 21:35:59.238 glog_line.go:8: 123
2019-05-22 21:35:59.239 /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_line.go:9: 123
```