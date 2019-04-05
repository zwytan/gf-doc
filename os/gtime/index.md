
[TOC]

通用时间管理模块，封装了常用的时间/日期相关的方法。并支持自定义的日期格式化语法，格式化语法灵感来源于`PHP`的`date`函数语法 ( http://php.net/manual/zh/function.date.php )。

**使用方式**：
```go
import "github.com/gogf/gf/g/os/gtime"
```

**接口文档**： 

https://godoc.org/github.com/gogf/gf/g/os/gtime

# 时间格式
`gtime`模块最大的特点是支持自定义的时间格式，以下是支持的时间格式语法列表：

![](/images/gtime-format2.png)


# 时间对象

创建`gtime.Time`对象可以通过标准库`time.Time`对象、Unix时间戳、时间字符串（如：`2018-07-18 12:01:00`）、自定义时间字符串（需要给定格式，支持自定义格式及标准库格式）。

## 示例1，自定义时间格式化语法
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/os/gtime"
)

func main() {
    formats := []string{
        "Y-m-d H:i:s.u",
        "D M d H:i:s T O Y",
        "\\T\\i\\m\\e \\i\\s: h:i:s a",
        "2006-01-02T15:04:05.000000000Z07:00",
    }
    t := gtime.Now()
    for _, f := range formats {
        fmt.Println(t.Format(f))
    }
}
```
在该示例中，我们给定了四种`format`格式，并将当前时间用这四种格式转换后打印出来。执行后，输出结果如下：
```html
2018-07-22 11:17:13.797
Sun Jul 22 11:17:13 CST +0800 2018
Time is: 11:17:13 am
2006-01-02CST15:04:05.000000000Z07:00
```
可以看到，这个示例演示了几个需要注意的地方：
1. 如果使用的字母与格式化字符冲突时，可以使用`\`符号转义该字符，这样时间格式解析器会认为该字符不是格式化字符，而是普通字母。因此这里的第三个字符串示例输出为：`Time is: 11:17:13 am`
2. 使用`Format`方法接收的是自定义的时间格式化语法（如：`Y-m-d H:i:s`），而非标准库的时间格式语法（如：`2006-01-02 15:04:05`），因此在这里的第四个字符串示例中原样输出参数值；

## 示例2，标准库时间格式化语法
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/os/gtime"
)

func main() {
    formats := []string{
        "2006-01-02 15:04:05.000",
        "Mon Jan _2 15:04:05 MST 2006",
        "Time is: 03:04:05 PM",
        "2006-01-02T15:04:05.000000000Z07:00 MST",
    }
    t := gtime.Now()
    for _, f := range formats {
        fmt.Println(t.Layout(f))
    }
}
```
在该示例中，我们使用四种标准库的时间格式化语法格式化当前的时间并输出结果到终端。执行后，输出结果为：
```html
2018-07-22 11:28:13.945
Sun Jul 22 11:28:13 CST 2018
Time is: 11:28:13 AM
2018-07-22T11:28:13.945153275+08:00 CST
```
有几个需要说明的地方：
1. 自定义时间格式化语法与标准库时间格式化语法并不冲突，前者使用`Format`方法，后者使用`Layout`方法行格式化，相互独立，互不冲突，无法混用；
2. 标准库的时间格式化语法自有特点，是不是感觉有点复杂；

## 示例3，时间对象链式操作
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/os/gtime"
    "time"
)

func main() {
    // 去年今日
    fmt.Println(gtime.Now().AddDate(-1, 0, 0).Format("Y-m-d"))

    // 去年今日，UTC时间
    fmt.Println(gtime.Now().AddDate(-1, 0, 0).Format("Y-m-d H:i:s T"))
    fmt.Println(gtime.Now().AddDate(-1, 0, 0).UTC().Format("Y-m-d H:i:s T"))

    // 下个月1号凌晨0点整
    fmt.Println(gtime.Now().AddDate(0, 1, 0).Format("Y-m-d 00:00:00"))

    // 1个小时前
    fmt.Println(gtime.Now().Add(-time.Hour).Format("Y-m-d H:i:s"))
}
```
执行后，输出结果为：
```html
2017-07-22
2017-07-22 11:42:36 CST
2017-07-22 03:42:36 UTC
2018-08-22 00:00:00
2018-07-22 10:42:36
```
该示例比较简单，便不多赘述。

# 工具方法

https://godoc.org/github.com/gogf/gf/g/os/gtime


方法比较简单，比较常用的是以下几个方法;
1. `Second`用于获得当前时间戳，`Millisecond`、`Microsecond`及`Nanosecond`用于获得当前的毫秒、微秒和纳秒值；
2. `Date`和`Datetime`用于获得当前日期及当前日期时间；
3. `SetTimeZone`用于设置当前进程的全局时区；
4. 其他方法说明请查看接口文档；

## 示例1，基本使用
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/os/gtime"
)

func main() {
    fmt.Println("Date       :", gtime.Date())
    fmt.Println("Datetime   :", gtime.Datetime())
    fmt.Println("Second     :", gtime.Second())
    fmt.Println("Millisecond:", gtime.Millisecond())
    fmt.Println("Microsecond:", gtime.Microsecond())
    fmt.Println("Nanosecond :", gtime.Nanosecond())
}
```
执行后，输出结果为：
```html
Date       : 2018-07-22
Datetime   : 2018-07-22 11:52:22
Second     : 1532231542
Millisecond: 1532231542688
Microsecond: 1532231542688688
Nanosecond : 1532231542688690259
```

## 示例2，设置时区
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/os/gtime"
    "time"
)

func main() {
    // 先使用标准库打印当前时间
    fmt.Println(time.Now().String())
    // 设置进程全局时区
    err := gtime.SetTimeZone("Asia/Tokyo")
    if err != nil {
        panic(err)
    }
    // 使用gtime获取当前时间
    fmt.Println(gtime.Now().String())
    // 使用标准库获取当前时间
    fmt.Println(time.Now().String())
}
```
执行后，输出结果为：
```
2018-11-21 22:50:56.723429 +0800 CST m=+0.000649366
2018-11-21 23:50:56
2018-11-21 23:50:56.723832 +0900 JST m=+0.001052780
```

## 示例3，`StrToTime`

`gtime`支持常见的时间字符串解析，生成`gtime.Time`对象，常见的时间字符串如下：
```html
"2017-12-14 04:51:34 +0805 LMT",
"2017-12-14 04:51:34 +0805 LMT",
"2006-01-02T15:04:05Z07:00",
"2014-01-17T01:19:15+08:00",
"2018-02-09T20:46:17.897Z",
"2018-02-09 20:46:17.897",
"2018-02-09T20:46:17Z",
"2018-02-09 20:46:17",
"2018/10/31 - 16:38:46"
"2018-02-09",
"2018.02.09",

01-Nov-2018 11:50:28
01/Nov/2018 11:50:28
01.Nov.2018 11:50:28
01.Nov.2018:11:50:28
日期连接符号支持'-'、'/'、'.'
```

使用示例：
```go
package main

import (
	"fmt"
	"github.com/gogf/gf/g/os/glog"
	"github.com/gogf/gf/g/os/gtime"
	"time"
)

func main() {
	array := []string{
		"2017-12-14 04:51:34 +0805 LMT",
		"2006-01-02T15:04:05Z07:00",
		"2014-01-17T01:19:15+08:00",
		"2018-02-09T20:46:17.897Z",
		"2018-02-09 20:46:17.897",
		"2018-02-09T20:46:17Z",
		"2018-02-09 20:46:17",
		"2018.02.09 20:46:17",
		"2018-02-09",
		"2017/12/14 04:51:34 +0805 LMT",
		"2018/02/09 12:00:15",
		"01/Nov/2018:13:28:13 +0800",
		"01-Nov-2018 11:50:28 +0805 LMT",
		"01-Nov-2018T15:04:05Z07:00",
		"01-Nov-2018T01:19:15+08:00",
		"01-Nov-2018 11:50:28 +0805 LMT",
		"01/Nov/2018 11:50:28",
		"01/Nov/2018:11:50:28",
		"01.Nov.2018:11:50:28",
		"01/Nov/2018",
	}
	cstLocal, _ := time.LoadLocation("Asia/Shanghai")
	for _, s := range array {
		if t, err := gtime.StrToTime(s); err == nil {
			fmt.Println(s)
			fmt.Println(t.UTC().String())
			fmt.Println(t.In(cstLocal).String())
		} else {
			glog.Error(s, err)
		}
		fmt.Println()
	}
}
```
在这个示例中，将部分时间格式串使用`StrToTime`方法转换为`gtime.Time`对象，并输出该事件的`UTC`时间和`CST`时间(上海时区时间)。
执行后，输出结果为：
```html
2017-12-14 04:51:34 +0805 LMT
2017-12-13 20:46:34
2017-12-14 04:46:34 +0800 CST

2006-01-02T15:04:05Z07:00
2006-01-02 22:04:05
2006-01-03 06:04:05 +0800 CST

2014-01-17T01:19:15+08:00
2014-01-16 17:19:15
2014-01-17 01:19:15 +0800 CST

2018-02-09T20:46:17.897Z
2018-02-09 20:46:17
2018-02-10 04:46:17.897 +0800 CST

2018-02-09 20:46:17.897
2018-02-09 12:46:17
2018-02-09 20:46:17.897 +0800 CST

2018-02-09T20:46:17Z
2018-02-09 20:46:17
2018-02-10 04:46:17 +0800 CST

2018-02-09 20:46:17
2018-02-09 12:46:17
2018-02-09 20:46:17 +0800 CST

2018.02.09 20:46:17
2018-02-09 12:46:17
2018-02-09 20:46:17 +0800 CST

2018-02-09
2018-02-08 16:00:00
2018-02-09 00:00:00 +0800 CST

2017/12/14 04:51:34 +0805 LMT
2017-12-13 20:46:34
2017-12-14 04:46:34 +0800 CST

2018/02/09 12:00:15
2018-02-09 04:00:15
2018-02-09 12:00:15 +0800 CST

01/Nov/2018:13:28:13 +0800
2018-11-01 05:28:13
2018-11-01 13:28:13 +0800 CST

01-Nov-2018 11:50:28 +0805 LMT
2018-11-01 03:45:28
2018-11-01 11:45:28 +0800 CST

01-Nov-2018T15:04:05Z07:00
2018-11-01 22:04:05
2018-11-02 06:04:05 +0800 CST

01-Nov-2018T01:19:15+08:00
2018-10-31 17:19:15
2018-11-01 01:19:15 +0800 CST

01-Nov-2018 11:50:28 +0805 LMT
2018-11-01 03:45:28
2018-11-01 11:45:28 +0800 CST

01/Nov/2018 11:50:28
2018-11-01 03:50:28
2018-11-01 11:50:28 +0800 CST

01/Nov/2018:11:50:28
2018-11-01 03:50:28
2018-11-01 11:50:28 +0800 CST

01.Nov.2018:11:50:28
2018-11-01 03:50:28
2018-11-01 11:50:28 +0800 CST

01/Nov/2018
2018-10-31 16:00:00
2018-11-01 00:00:00 +0800 CST
```

