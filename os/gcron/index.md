# gcron

`gcron`模块提供了对定时任务的实现，支持类似`crontab`的配置管理方式，并支持最小粒度到`秒`的定时任务管理。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gcron"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/os/gcron](https://godoc.org/github.com/johng-cn/gf/g/os/gcron)

```go
func Add(spec string, f func(), name...string) error
func Remove(name string)
func Search(name string) *Entry
func Entries() []Entry
```
简要说明：
1. `Add`方法用于添加定时任务，其中：
    - `spec` 参数使用`CRON语法格式`(具体说明见本章后续相关说明)；
    - `f` 参数为需要执行的任务方法(方法地址)；
    - `name` 为非必需参数，用于给定时任务指定一个**唯一的**名称，注意如果已存在相同名称的任务，那么添加定时任务将会失败；
1. `Remove`方法用于根据名称删除定时任务；
1. `Entries`方法用于获取当前所有已注册的定时任务信息；
1. `Search`方法用于根据名称进行定时任务搜索；



## 使用示例

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/os/gcron"
    "time"
)

func main() {
    gcron.Add("0 30 * * * *", func() { fmt.Println("Every hour on the half hour") })
    gcron.Add("* * * * * *",  func() { fmt.Println("Every second") }, "second-cron")
    gcron.Add("@hourly",      func() { fmt.Println("Every hour") })
    gcron.Add("@every 1h30m", func() { fmt.Println("Every hour thirty") })
    g.Dump(gcron.Entries())

    time.Sleep(3*time.Second)

    gcron.Remove("second-cron")

    time.Sleep(3*time.Second)
}
```

## CRON表达格式

`cron表达式`表示一组时间，使用`6`个空格分隔的字段。

```
Field name   | Mandatory? | Allowed values  | Allowed special characters
----------   | ---------- | --------------  | --------------------------
Seconds      | Yes        | 0-59            | * / , -
Minutes      | Yes        | 0-59            | * / , -
Hours        | Yes        | 0-23            | * / , -
Day of month | Yes        | 1-31            | * / , - ?
Month        | Yes        | 1-12 or JAN-DEC | * / , -
Day of week  | Yes        | 0-6 or SUN-SAT  | * / , - ?
```

注意：月份和星期几字段值不区分大小写。 “SUN”，“Sun”，
和“sun”同样被接受。

### 特殊字符

#### 星号（`*`）

星号表示cron表达式将匹配所有的值。例如，在第5个字段（月）中使用星号表示每个字符
月。

#### 斜线（`/`）

斜杠用于描述范围的增量。例如`3-59/15`中第一个字段（分钟）表示每小时的第3分钟和每15分钟几分钟后。

#### 逗号（`,`）

逗号用于分隔列表的项目。例如，使用“MON，WED，FRI”第五个字段（星期几）将指周一，周三和周五。

#### 连字符（`-`）

连字符用于定义范围。例如，9-17表示每个
上午9点至下午5点（含）。

#### 问号（`?`）

可以使用问号而不是'*'来让日期或星期几字段空白。

#### 预定义的时间表

您可以使用几个预定义的时间来代替cron表达式。

```
Entry                  | Description                                | Equivalent To
-----                  | -----------                                | -------------
@yearly (or @annually) | Run once a year, midnight, Jan. 1st        | 0 0 0 1 1 *
@monthly               | Run once a month, midnight, first of month | 0 0 0 1 * *
@weekly                | Run once a week, midnight between Sat/Sun  | 0 0 0 * * 0
@daily (or @midnight)  | Run once a day, midnight                   | 0 0 0 * * *
@hourly                | Run once an hour, beginning of hour        | 0 0 * * * *
```

#### 间隔

您还可以定义任务以固定的时间间隔执行，从添加时开始运行。这可以通过格式化cron规范来支持，如下所示：
```
@every <duration>
```
其中`duration`是`time.ParseDuration`接受的字符串
（[http://golang.org/pkg/time/#ParseDuration](http://golang.org/pkg/time/#ParseDuration)）。

例如，`@every 1h30m10s`将表示添加任务之后每隔`1小时30分10秒`执行。

> 注意：间隔不会考虑任务的执行时间。例如，如果一项工作需要3分钟才能执行完成，并且计划每隔5分钟运行一次，那么每次任务之间只有2分钟的空闲时间。