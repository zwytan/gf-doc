# 链式操作

`glog`模块支持非常简便的`链式操作`方式，相关链式操作方法如下：
```go
// 是否开启终端输出
func StdPrint(enabled bool) *Logger
// 是否开启trace打印
func Backtrace(enabled bool) *Logger
// 设置日志文件分类
func Cat(category string) *Logger
// 设置日志文件格式
func File(file string) *Logger
// 设置日志打印级别
func Level(level int) *Logger
```

简单示例：
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
    glog.StdPrint(false).Cat("cat1").Cat("cat2").Println("test")
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