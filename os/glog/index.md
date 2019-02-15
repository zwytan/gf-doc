[TOC]

通用日志管理模块。

**使用方式**：
```go
import "github.com/gogf/gf/g/os/glog"
```

# 接口文档
[godoc.org/github.com/gogf/gf/g/os/glog](http://godoc.org/github.com/gogf/gf/g/os/glog)

## 包方法
```go
func Critical(v ...interface{})
func Criticalf(format string, v ...interface{})
func Criticalfln(format string, v ...interface{})
func Debug(v ...interface{})
func Debugf(format string, v ...interface{})
func Debugfln(format string, v ...interface{})
func Error(v ...interface{})
func Errorf(format string, v ...interface{})
func Errorfln(format string, v ...interface{})
func Fatal(v ...interface{})
func Fatalf(format string, v ...interface{})
func Fatalfln(format string, v ...interface{})
func Fatalln(v ...interface{})
func GetBacktrace() string
func GetLevel() int
func GetPath() string
func Info(v ...interface{})
func Infof(format string, v ...interface{})
func Infofln(format string, v ...interface{})
func Notice(v ...interface{})
func Noticef(format string, v ...interface{})
func Noticefln(format string, v ...interface{})
func Panic(v ...interface{})
func Panicf(format string, v ...interface{})
func Panicfln(format string, v ...interface{})
func Panicln(v ...interface{})
func Print(v ...interface{})
func PrintBacktrace()
func Printf(format string, v ...interface{})
func Printfln(format string, v ...interface{})
func Println(v ...interface{})
func SetDebug(debug bool)
func SetFile(file string)
func SetLevel(level int)
func SetPath(path string)
func SetStdPrint(open bool)
func Warning(v ...interface{})
func Warningf(format string, v ...interface{})
func Warningfln(format string, v ...interface{})
```
## 对象方法
```go
type Logger
    func Backtrace(enabled bool) *Logger
    func Cat(category string) *Logger
    func File(file string) *Logger
    func Level(level int) *Logger
    func New() *Logger
    func StdPrint(enabled bool) *Logger
    func (l *Logger) Backtrace(enabled bool) *Logger
    func (l *Logger) Cat(category string) *Logger
    func (l *Logger) Clone() *Logger
    func (l *Logger) Critical(v ...interface{})
    func (l *Logger) Criticalf(format string, v ...interface{})
    func (l *Logger) Criticalfln(format string, v ...interface{})
    func (l *Logger) Debug(v ...interface{})
    func (l *Logger) Debugf(format string, v ...interface{})
    func (l *Logger) Debugfln(format string, v ...interface{})
    func (l *Logger) Error(v ...interface{})
    func (l *Logger) Errorf(format string, v ...interface{})
    func (l *Logger) Errorfln(format string, v ...interface{})
    func (l *Logger) Fatal(v ...interface{})
    func (l *Logger) Fatalf(format string, v ...interface{})
    func (l *Logger) Fatalfln(format string, v ...interface{})
    func (l *Logger) Fatalln(v ...interface{})
    func (l *Logger) File(file string) *Logger
    func (l *Logger) GetBacktrace() string
    func (l *Logger) GetIO() io.Writer
    func (l *Logger) GetLevel() int
    func (l *Logger) Info(v ...interface{})
    func (l *Logger) Infof(format string, v ...interface{})
    func (l *Logger) Infofln(format string, v ...interface{})
    func (l *Logger) Level(level int) *Logger
    func (l *Logger) Notice(v ...interface{})
    func (l *Logger) Noticef(format string, v ...interface{})
    func (l *Logger) Noticefln(format string, v ...interface{})
    func (l *Logger) Panic(v ...interface{})
    func (l *Logger) Panicf(format string, v ...interface{})
    func (l *Logger) Panicfln(format string, v ...interface{})
    func (l *Logger) Panicln(v ...interface{})
    func (l *Logger) Print(v ...interface{})
    func (l *Logger) PrintBacktrace()
    func (l *Logger) Printf(format string, v ...interface{})
    func (l *Logger) Printfln(format string, v ...interface{})
    func (l *Logger) Println(v ...interface{})
    func (l *Logger) SetBacktrace(enabled bool)
    func (l *Logger) SetDebug(debug bool)
    func (l *Logger) SetFile(file string)
    func (l *Logger) SetIO(w io.Writer)
    func (l *Logger) SetLevel(level int)
    func (l *Logger) SetPath(path string) error
    func (l *Logger) SetStdPrint(enabled bool)
    func (l *Logger) StdPrint(enabled bool) *Logger
    func (l *Logger) Warning(v ...interface{})
    func (l *Logger) Warningf(format string, v ...interface{})
    func (l *Logger) Warningfln(format string, v ...interface{})
```
重要的几点说明：
1. `glog`是并发安全的，无论是创建的`glog.Logger`对象，还是提供的包方法；
1. `glog`支持文件输出、日志级别、日志分类、调试管理、调用跟踪、链式操作等丰富特性；
1. 可以使用`glog.New`方法创建`glog.Logger`对象用于自定义日志打印，也可以使用`glog`默认提供的包方法来打印日志。当使用包方法时，注意任何的`glog.Set*`设置方法都将会**全局生效**；
1. 日志内容格式固定为 `时间 [级别] 内容 换行`，其中`时间`精确到毫秒级别，`级别`为可选输出，`内容`为调用端的参数输入，`换行`为可选输出(部分方法自动为日志内容添加换行符号)，日志内容例如：`2018-10-10 12:00:01.568 [ERRO] 产生错误`；
2. `Print*/Debug*/Info*`方法输出日志到标准输出(`stdout`)，`Notice*/Warning*/Error*/Critical*/Panic*/Fatal*`方法输出日志到标准错误(`stderr`)；
3. 其中`Panic*`方法在输出日志信息后会引发`panic`错误方法，`Fatal*`方法在输出日志信息之后会停止进程运行，并返回进程状态码值为`1`(正常程序退出状态码为`0`)；

# 日志级别

日志级别用于管理日志的输出，我们可以通过设定特定的日志级别来关闭/开启特定的日志内容。通过`SetLevel`方法可以设置日志级别，`glog`支持以下几种日志级别常量设定：
```go
LEVEL_ALL  
LEVEL_DEBU 
LEVEL_INFO
LEVEL_NOTI
LEVEL_WARN
LEVEL_ERRO
LEVEL_CRIT
```
我们可以通过`位操作`组合使用这几种级别，例如其中`LEVEL_ALL`等价于`LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT`。例如我们可以通过`LEVEL_ALL & ^LEVEL_DEBU & ^LEVEL_INFO & ^LEVEL_NOTI`来过滤掉`LEVEL_DEBU/LEVEL_INFO/LEVEL_NOTI`日志内容。

简单示例：

```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
)

// 设置日志等级，过滤掉Info日志信息
func main() {
    l := glog.New()
    l.Info("info1")
    l.SetLevel(glog.LEVEL_ALL^glog.LEVEL_INFO)
    l.Info("info2")
}
```
执行后，输出结果为：
```html
2018-10-10 14:38:48.687 [INFO] info1
```

# 日志目录

默认情况下，`glog`将会输出日志内容到标准输出，我们可以通过`SetPath`方法设置日志输出的目录路径，这样日志内容将会写入到日志文件中，并且由于其内部使用了`gfpool`文件指针池，文件写入的效率相当优秀。简单示例：

```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gfile"
    "github.com/gogf/gf/g"
)

// 设置日志输出路径
func main() {
    path := "/tmp/glog"
    glog.SetPath(path)
    glog.Println("日志内容")
    list, err := gfile.ScanDir(path, "*")
    g.Dump(err)
    g.Dump(list)
}
```
执行后，输出内容为：
```html
2018-10-10 14:03:46.904 日志内容
null
[
	"/tmp/glog/2018-10-10.log"
]
```
当通过`SetPath`设置日志的输出目录，如果目录不存在时，将会递归创建该目录路径。可以看到，执行`Println`之后，在`/tmp`下创建了日志目录`glog`，并且在其下生成了日志文件。同时，我们也可以看见日志内容不仅输出到了文件，默认情况下也输出到了终端，我们可以通过`SetStdPrint(false)`方法来关闭终端的日志输出，这样日志内容仅会输出到日志文件中。

> 我们也可以通过gf框架的`g.SetLogLevel`/`g.SetDebug`模块来管理日志级别、调试信息。对应的日志等级如下：
```
LOG_LEVEL_ALL
LOG_LEVEL_DEBU
LOG_LEVEL_INFO
LOG_LEVEL_NOTI
LOG_LEVEL_WARN
LOG_LEVEL_ERRO
LOG_LEVEL_CRIT
```

# 日志文件

默认情况下，日志文件名称以当前时间日期命名，格式为`YYYY-MM-DD.log`，我们可以使用`SetFile`方法来设置文件名称的格式，并且文件名称格式支持【[`gtime`时间格式](os/gtime/index.md)】。简单示例：

```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gfile"
    "github.com/gogf/gf/g"
)

// 设置日志等级
func main() {
    path := "/tmp/glog"
    glog.SetPath(path)
    glog.SetStdPrint(false)
    // 使用默认文件名称格式
    glog.Println("标准文件名称格式，使用当前时间时期")
    // 通过SetFile设置文件名称格式
    glog.SetFile("stdout.log")
    glog.Println("设置日志输出文件名称格式为同一个文件")
    // 链式操作设置文件名称格式
    glog.File("stderr.log").Println("支持链式操作")
    glog.File("error-{Ymd}.log").Println("文件名称支持带gtime日期格式")
    glog.File("access-{Ymd}.log").Println("文件名称支持带gtime日期格式")

    list, err := gfile.ScanDir(path, "*")
    g.Dump(err)
    g.Dump(list)
}
```
执行后，输出结果为：
```html
null
[
	"/tmp/glog/2018-10-10.log",
	"/tmp/glog/access-20181010.log",
	"/tmp/glog/error-20181010.log",
	"/tmp/glog/stderr.log",
	"/tmp/glog/stdout.log"
]
```
可以看到，文件名称格式中如果需要使用`gtime`时间格式，格式内容需要使用`{xxx}`包含起来。该示例中也使用到了`链式操作`的特性，具体请看后续说明。

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


# Trace特性

错误日志信息支持`trace`特性，可以通过`Notice*/Warning*/Error*/Critical*/Panic*/Fatal*`错误方法触发，也可以通过`GetBacktrace/PrintBacktrace`获取/打印。错误信息的`trace`信息对于调试错误来说相当有用。

## 示例1，通过错误方法触发
```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
)

func main() {
    glog.Error("发生错误！")
}
```
打印出的结果如下：
```html
2018-01-11 17:20:10 [ERRO] 发生错误！
Trace:
1. /home/john/Workspace/Go/github.com/gogf/gf/geg/other/test.go:8
```

## 示例2，通过trace方法打印

```go
package main

import (
    "github.com/gogf/gf/g/os/glog"
    "fmt"
)

func main() {

    glog.PrintBacktrace()
    glog.New().PrintBacktrace()

    fmt.Println(glog.GetBacktrace())
    fmt.Println(glog.New().GetBacktrace())
}
```
执行后，输出结果为：
```html
2018-10-10 15:18:34.346 1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:10

2018-10-10 15:18:34.346 1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:11

1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:13

1. /home/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/geg/os/glog/glog_backtrace.go:14
```


# Debug特性

`Debug/Debugf/Debugfln`是非常有用的几个方法，用于调试信息的记录，常用于开发/测试环境中，当应用上线之后可以方便地使用`SetDebug`进行开启/关闭。

```go
package main

import (
    "time"
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gtime"
)

func main() {
    gtime.SetTimeout(3*time.Second, func() {
        glog.SetDebug(false)
    })
    for {
        glog.Debug(gtime.Datetime())
        time.Sleep(time.Second)
    }
}
```
该示例中使用```glog.Debug```方法输出调试信息，3秒后关闭调试信息的输出。执行后，输出结果如下，可以看到只输出了3条日志信息，后续的调试日志信息由于通过`SetDebug`方法关闭后，便不再输出。
```html
2018-07-22 12:20:11.100 [DEBU] 2018-07-22 12:20:11
2018-07-22 12:20:12.101 [DEBU] 2018-07-22 12:20:12
2018-07-22 12:20:13.101 [DEBU] 2018-07-22 12:20:13
```

> 我们还可以通过命令行参数或者系统环境变量参数的方式关闭掉调试信息。
1. 修改命令行启动参数 - ```gf.glog.debug=false```；
1. 修改指定的环境变量 - ```GF_GLOG_DEBUG=false```；



