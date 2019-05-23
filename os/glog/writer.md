
# 自定义`Writer`接口

`glog`模块实现了标准输出以及文件输出的日志内容打印。当然，开发者也可以通过自定义`io.Writer`接口实现自定义的日志内容输出。`io.Writer`是标准库提供的内容输出接口，其定义如下：

```go
type Writer interface {
	Write(p []byte) (n int, err error)
}
```

我们可以通过`SetWriter`方法或者链式方法`To`来实现自定义`Writer`，在该`Writer`中实现定义的操作，也可以整合其他的模块功能。

此外，`glog.Logger`对象已经实现了`io.Writer`接口，因此可以非常方便地将`glog`整合使用到其他的模块中。

# 使用示例1，实现日志`HOOK`

在该示例中，我们实现了一个自定义的`Writer`对象`MyWriter`，在该对象实现的`Writer`接口中我们对日志内容进行判断，如果出现了`PANI`或者`FATA`错误，那么表示是非常严重的错误，该接口将会第一时间通过`HTTP`接口告知`Monitor`监控服务。随后再将日志内容通过`glog`模块按照配置写入到文件和标准输出。

```go
package main

import (
	"fmt"
	"github.com/gogf/gf/g/net/ghttp"
	"github.com/gogf/gf/g/os/glog"
	"github.com/gogf/gf/g/text/gregex"
)

type MyWriter struct {
	logger *glog.Logger
}

func (w *MyWriter) Write(p []byte) (n int, err error) {
	s := string(p)
	if gregex.IsMatchString(`\[(PANI|FATA)\]`, s) {
		fmt.Println("SERIOUS ISSUE OCCURRED!! I'd better tell monitor in first time!")
		ghttp.PostContent("http://monitor.mydomain.com", s)
	}
	return w.logger.Write(p)
}

func main() {
	glog.SetWriter(&MyWriter{
		logger : glog.New(),
	})
	glog.Fatal("FATAL ERROR")
}
```
执行后，输出结果为：
```html
SERIOUS ISSUE OCCURRED!! I'd better tell monitor in first time!
2019-05-23 20:14:49.374 [FATA] FATAL ERROR
Backtrace:
1. /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_writer_hook.go:27
```





