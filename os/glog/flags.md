# 额外特性

`flags`用于控制日志组件的额外特性开关，这些属性使用常量进行组合控制，包括：
```go
F_FILE_LONG  = 1 << iota // 打印调用文件信息，完整绝对路径，例如：/a/b/c/d.go:23
F_FILE_SHORT             // 打印调用文件信息，仅打印文件名，例如：d.go:23，覆盖 F_FILE_LONG.
F_TIME_DATE              // 打印当前日期，如：2009-01-23
F_TIME_TIME              // 打印当前时间，如：01:23:23
F_TIME_MILLI             // 打印当前时间+毫秒，如：01:23:23.675
F_TIME_STD = F_TIME_DATE | F_TIME_MILLI // (默认)打印当前日期+时间+毫秒，如：2009-01-23 01:23:23.675
```
使用示例：
```go
package main

import (
	"github.com/gogf/gf/g/os/glog"
)

func main() {
	l := glog.New()
	l.SetFlags(glog.F_TIME_TIME|glog.F_FILE_SHORT)
	l.Println("123")
	l.SetFlags(glog.F_TIME_MILLI|glog.F_FILE_LONG)
	l.Println("123")
	l.SetFlags(glog.F_TIME_STD|glog.F_FILE_LONG)
	l.Println("123")
}
```
执行后，终端输出结果为：
```html
21:41:38 glog_flags.go:10: 123
21:41:38.717 /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_flags.go:12: 123
2019-05-22 21:41:38.717 /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_flags.go:14: 123
```
