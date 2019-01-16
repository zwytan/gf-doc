# gtimer

`gtimer`是一个高性能的任务定时器，类似于Java的`Timer`，但是比较于Java的`Timer`更加强大，内部实现采用灵活高效的分层时间轮算法，被设计为可管理维护百万级别数量的定时任务。

`gtimer`与`gcron`定时任务模块的区别是:
- `gtimer`属于高性能模块，是框架的核心模块，构建任何定时任务的基础，任何方法操作都是在`纳秒`级别；
- `gtimer`可适用于任何的定时任务场景中，例如: TCP通信、游戏研发等，最小间隔时间粒度为`毫秒`级别；
- `gcron`支持`crontab`形式的定时任务语法，可读性较高，适用于应用层研发，最小时间间隔为`秒`；
- `gcron`底层实现基于`gtimer`；

**使用场景**：

任何定时任务场景，大数据量的定时任务场景，对于定时时间准确度要求不是很高的业务场景。

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
    func (t *Timer) Close()
    func (t *Timer) DelayAdd(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddOnce(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddSingleton(delay time.Duration, interval time.Duration, job JobFunc)
    func (t *Timer) DelayAddTimes(delay time.Duration, interval time.Duration, times int, job JobFunc)
type Entry
    func (entry *Entry) Close()
    func (entry *Entry) IsSingleton() bool
    func (entry *Entry) Run()
    func (entry *Entry) SetSingleton(enabled bool)
    func (entry *Entry) SetStatus(status int) int
    func (entry *Entry) SetTimes(times int)
    func (entry *Entry) Status() int
```

## 时间轮设计

`gtimer`采用















