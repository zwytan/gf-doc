
[TOC]

通用时间管理模块，封装了常用的时间/日期相关的方法。并支持自定义的日期格式化语法，格式化语法类似PHP的date语法。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gtime"
```

接口文档： [godoc.org/github.com/gogf/gf/g/os/gtime](https://godoc.org/github.com/gogf/gf/g/os/gtime)

# 时间格式
`gtime`模块最大的特点是支持自定义的时间格式，参考[PHP日期时间格式语法](http://php.net/manual/zh/function.date.php)，以下是支持的时间格式语法列表：

![](/images/gtime-format2.png)



# 时间对象
接口文档：
```go
type Time
    func New(t ...time.Time) *Time
    func NewFromStr(str string) *Time
    func NewFromStrFormat(str string, format string) *Time
    func NewFromStrLayout(str string, layout string) *Time
    func NewFromTime(t time.Time) *Time
    func NewFromTimeStamp(timestamp int64) *Time
    func Now() *Time
    func (t *Time) Add(d time.Duration) *Time
    func (t *Time) AddDate(years int, months int, days int) *Time
    func (t *Time) Clone() *Time
    func (t *Time) Format(format string) string
    func (t *Time) Layout(layout string) string
    func (t *Time) Local() *Time
    func (t *Time) Microsecond() int64
    func (t *Time) Millisecond() int64
    func (t *Time) Nanosecond() int64
    func (t *Time) Round(d time.Duration) *Time
    func (t *Time) Second() int64
    func (t *Time) String() string
    func (t *Time) ToLocation(location *time.Location) *Time
    func (t *Time) ToTime() time.Time
    func (t *Time) Truncate(d time.Duration) *Time
    func (t *Time) UTC() *Time
```
创建```gtime.Time```对象可以通过标准库```time.Time```对象、Unix时间戳、时间字符串（如：2018-07-18 12:01:00）、自定义时间字符串（需要给定格式，支持自定义格式及标准库格式）。

## 示例1，自定义时间格式化语法
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
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
在该示例中，我们给定了四种format格式，并将当前时间用这四种格式转换后打印出来。执行后，输出结果如下：
```html
2018-07-22 11:17:13.797
Sun Jul 22 11:17:13 CST +0800 2018
Time is: 11:17:13 am
2006-01-02CST15:04:05.000000000Z07:00
```
可以看到，这个示例演示了几个需要注意的地方：
1. 如果使用的字母与格式化字符冲突时，可以使用```\```符号转移该字符，这样时间格式解析器会认为该字符不是格式化字符，而是普通字母。因此这里的第三个字符串示例输出为：```Time is: 11:17:13 am```
2. 使用```Format```方法接收的是自定义的时间格式化语法（如：```Y-m-d H:i:s```），而不是标准库的事件格式语法（如：```2006-01-02 15:04:05```），且两种语法不能混用，因此在这里的第四个字符串示例中原样输出参数值；

## 示例2，标准库时间格式化语法
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
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
根绝这个示例，也有几个需要说明的地方：
1. 自定义时间格式化语法与标准库时间格式化语法并不冲突，前者使用```Format```方法，后者使用```Layout```语法进行格式化，相互独立，互不冲突，无法混用；
2. 标准库的时间格式化语法自有特点，是不是感觉有点复杂；

## 示例3，时间对象链式操作
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
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

    // 2个小时前
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
[godoc.org/github.com/gogf/gf/g/os/gtime](https://godoc.org/github.com/gogf/gf/g/os/gtime)
```go
func Date() string
func Datetime() string
func Microsecond() int64
func Millisecond() int64
func Nanosecond() int64
func Second() int64
func SetTimeZone(zone string) error
func StrToTime(str string) (time.Time, error)
func StrToTimeFormat(str string, format string) (time.Time, error)
func StrToTimeLayout(str string, layout string) (time.Time, error)
func ParseTimeFromContent(content string, format...string) *Time
```
方法比较简单，比较常用的是以下几个方法;
1. ```Second```用于获得当前时间戳，```Millisecond```、```Microsecond```及```Nanosecond```用于获得当前的毫秒、微秒和纳秒值；
2. ```Date```和```Datetime```用于获得当前日期及当前日期时间；
3. ```SetTimeZone```用于设置当前进程的全局时区；
4. 其他方法说明请查看接口文档；

## 示例1，基本使用
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
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
    "gitee.com/johng/gf/g/os/gtime"
    "time"
)

func main() {
    // 先使用标准库打印当前时间
    fmt.Println(time.Now().String())
    // 设置进程时区，全局有效
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
