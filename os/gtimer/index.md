# gtimer

`gtimer`是一个高性能的任务定时器，类似于Java的`Timer`，但是比较于Java的`Timer`更加强大，内部实现采用灵活高效的分层时间轮设计，被设计为可管理维护百万级别以上数量的定时任务。

`gtimer`任务定时器与`gcron`定时任务模块区别:
- `gtimer`属于高性能模块，是框架的核心模块，构建任何定时任务的基础，任何方法操作都是在`纳秒`级别；
- `gtimer`可适用于任何的定时任务场景中，例如: TCP通信、游戏研发等，最小间隔时间粒度为`毫秒`级别；
- `gcron`支持`crontab`形式的复杂定时任务语法，适用于应用层研发，最小时间间隔为`秒`；
- `gcron`底层实现基于`gtimer`；

**使用场景**：

任何定时任务场景，延迟任务场景，大批量定时任务的场景，对于定时时间准确度要求不高的业务场景。

**注意事项**：

任何的定时任务都是有误差的，在并发量大，负载较高的系统中尤其明显，具体请参考：[https://github.com/golang/go/issues/14410](https://github.com/golang/go/issues/14410)

**使用方式**：
```go
import "gitee.com/johng/gf/g/os/gtimer"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/os/gtimer](https://godoc.org/github.com/johng-cn/gf/g/os/gtimer)

```go
func Add(interval time.Duration, job JobFunc) *Entry
func AddOnce(interval time.Duration, job JobFunc) *Entry
func AddSingleton(interval time.Duration, job JobFunc) *Entry
func AddTimes(interval time.Duration, times int, job JobFunc) *Entry
func DelayAdd(delay time.Duration, interval time.Duration, job JobFunc)
func DelayAddOnce(delay time.Duration, interval time.Duration, job JobFunc)
func DelayAddSingleton(delay time.Duration, interval time.Duration, job JobFunc)
func DelayAddTimes(delay time.Duration, interval time.Duration, times int, job JobFunc)
func SetInterval(interval time.Duration, job JobFunc)
func SetTimeout(delay time.Duration, job JobFunc)
func Exit()
type Timer
    func New(slot int, interval time.Duration, level ...int) *Timer
    func (t *Timer) Add(interval time.Duration, job JobFunc) *Entry
    func (t *Timer) AddOnce(interval time.Duration, job JobFunc) *Entry
    func (t *Timer) AddSingleton(interval time.Duration, job JobFunc) *Entry
    func (t *Timer) AddTimes(interval time.Duration, times int, job JobFunc) *Entry
    func (t *Timer) DelayAdd(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddOnce(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddSingleton(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddTimes(delay time.Duration, interval time.Duration, times int, job JobFunc)
    func (t *Timer) Start()
    func (t *Timer) Stop()
    func (t *Timer) Close()
type Entry
    func (entry *Entry) IsSingleton() bool
    func (entry *Entry) Run()
    func (entry *Entry) SetSingleton(enabled bool)
    func (entry *Entry) SetStatus(status int) int
    func (entry *Entry) SetTimes(times int)
    func (entry *Entry) Status() int
    func (entry *Entry) Start()
    func (entry *Entry) Stop()
    func (entry *Entry) Close()
```
简要说明：
1. `New`方法用于创建自定义的任务定时器对象:
    - `slot` 参数用于指定每个时间轮的槽数；
    - `interval` 参数用于指定定时器的最小tick时间间隔；
    - `level` 为非必需参数，用于自定义分层时间轮的层数，默认为`6`；
1. `Add`方法用于添加定时任务，其中：
    - `interval` 参数用于指定方法的执行的时间间隔；
    - `job` 参数为需要执行的任务方法(方法地址)；
1. `AddSingleton`方法用于添加单例定时任务，即同时只能有一个该任务正在运行；
1. `AddOnce`方法用于添加只运行一次的定时任务，当运行一次数后该定时任务自动销毁；
1. `AddTimes`方法用于添加运行指定次数的定时任务，当运行`times`次数后该定时任务自动销毁；
1. `Search`方法用于根据名称进行定时任务搜索(返回定时任务`*Entry`对象指针)；
1. `Start`方法用于启动定时任务(`Add`后自动启动定时任务)；
1. `Stop`方法用于停止定时任务；
1. `Close`方法用于关闭定时器；

## 时间轮设计
<div align=center>
<img src="images/hierarchical-timing-wheel.png" />
</div>

`gtimer`采用的是分层时间轮设计，相比较于单层时间轮设计，可以更高效地管理更多的定时任务。

关于时间轮的介绍，可以查看以下几个链接：
* http://www.embeddedlinux.org.cn/RTConforEmbSys/5107final/LiB0071.html
* https://www.slideshare.net/supperniu/timing-wheels

使用`gtimer`的默认定时器时，默认的时间轮槽数为`10`，间隔时间为`50ms`，分层大小为`6`，每一层的时间轮信息如下：

| 层级 | 槽数 | 间隔 | 一周大小
|---|---|---| ---
|0 | 10 | 50ms      | 500ms
|1 | 10 | 500ms     | 5000ms
|2 | 10 | 5000ms    | 50000ms
|3 | 10 | 50000ms   | 500000ms
|4 | 10 | 500000ms  | 5000000ms
|5 | 10 | 5000000ms | 50000000ms

相当于底层的时间轮转一圈，上层的时间轮才会移动一个刻度(执行一次任务检查)。

时间间隔比较大的定时任务将会被添加到高层的时间轮，当刻度指向该任务的槽时，会将该任务移动到低层的时间轮继续运行。

## 性能基准测试

```
goos: darwin
goarch: amd64
pkg: gitee.com/johng/gf/g/os/gtimer
Benchmark_Add-4               3000000             484 ns/op         152 B/op           5 allocs/op
Benchmark_StartStop-4       100000000            10.1 ns/op           0 B/op           0 allocs/op
PASS
ok      command-line-arguments    6.602s
```

## 使用示例1, 间隔任务

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/gtimer"
    "time"
)

func main() {
    now      := time.Now()
    interval := 1400*time.Millisecond
    gtimer.Add(interval, func() {
        fmt.Println(time.Now(), time.Duration(time.Now().UnixNano() - now.UnixNano()))
        now = time.Now()
    })

    select { }
}
```
执行后，输出结果为:
```
2019-01-17 18:17:37.022442 +0800 CST m=+1.354132542 1.353568s
2019-01-17 18:17:38.422467 +0800 CST m=+2.754148119 1.399624s
2019-01-17 18:17:39.82318 +0800 CST m=+4.154851847 1.40066s
2019-01-17 18:17:41.222422 +0800 CST m=+5.554084408 1.399094s
2019-01-17 18:17:42.622461 +0800 CST m=+6.954112968 1.399962s
2019-01-17 18:17:44.022769 +0800 CST m=+8.354411089 1.400251s
...
```

## 使用示例2, 单例任务

```go
package main

import (
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/os/gtimer"
    "time"
)

func main() {
    interval := time.Millisecond
    gtimer.AddSingleton(interval, func() {
        glog.Println("doing")
        time.Sleep(2*time.Second)
    })

    select { }
}
```
执行后，输出结果为:
```
2019-01-17 18:19:42.678 doing
2019-01-17 18:19:44.678 doing
2019-01-17 18:19:46.728 doing
2019-01-17 18:19:48.731 doing
2019-01-17 18:19:50.782 doing
...
```

















