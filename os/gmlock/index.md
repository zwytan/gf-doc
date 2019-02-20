[TOC]


内存锁。该模块包含两个对象特性：
1. `Locker` 内存锁，支持按照`给定键名生成内存锁`，并支持`Try*Lock`及`锁过期`特性；
1. `Mutex` 对标准库底层`sync.Mutex`的封装，增加了`Try*Lock`特性；

**使用方式**：
```go
import "github.com/gogf/gf/g/os/gmlock"
```


**使用场景**：
1. 任何需要并发安全的场景，可以替代`sync.Mutex`；
1. 需要使用`Try*Lock`的场景(不需要阻塞等待锁释放)；
1. 需要`动态创建互斥锁`，或者需要`维护大量动态锁`的场景；

**接口文档**：

[godoc.org/github.com/gogf/gf/g/os/gmlock](https://godoc.org/github.com/gogf/gf/g/os/gmlock)



# 示例1，基本使用
```go
package main

import (
    "time"
    "sync"
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gmlock"
)

func main() {
    key := "lock"
    wg  := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(i int) {
            gmlock.Lock(key)
            glog.Println(i)
            time.Sleep(time.Second)
            gmlock.Unlock(key)
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```
该示例中，模拟了同时开启`10`个goroutine，但同一时刻只能有一个goroutine获得锁，获得锁的goroutine执行1秒后退出，其他goroutine才能获得锁。

执行后，输出结果为：
```html
2018-10-15 23:57:28.295 9
2018-10-15 23:57:29.296 0
2018-10-15 23:57:30.296 1
2018-10-15 23:57:31.296 2
2018-10-15 23:57:32.296 3
2018-10-15 23:57:33.297 4
2018-10-15 23:57:34.297 5
2018-10-15 23:57:35.297 6
2018-10-15 23:57:36.298 7
2018-10-15 23:57:37.298 8
```

# 示例2，过期控制

我们将以上的示例使用过期时间控制来实现。

```go
package main

import (
    "sync"
    "github.com/gogf/gf/g/os/glog"
    "github.com/gogf/gf/g/os/gmlock"
)

func main() {
    key := "lock"
    wg  := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(i int) {
            gmlock.Lock(key, 1000)
            glog.Println(i)
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```
执行后，输出结果为：
```html
2018-10-15 23:59:14.663 9
2018-10-15 23:59:15.663 4
2018-10-15 23:59:16.663 0
2018-10-15 23:59:17.664 1
2018-10-15 23:59:18.664 2
2018-10-15 23:59:19.664 3
2018-10-15 23:59:20.664 6
2018-10-15 23:59:21.664 5
2018-10-15 23:59:22.665 7
2018-10-15 23:59:23.665 8
```

# 示例3，TryLock非阻塞锁

`TryLock`方法是有返回值的，它表示用来尝试获取锁，如果获取成功，则返回`true`；如果获取失败（即锁已被其他goroutine获取），则返回`false`。

```go
package main

import (
    "sync"
    "github.com/gogf/gf/g/os/glog"
    "time"
    "github.com/gogf/gf/g/os/gmlock"
)

func main() {
    key := "lock"
    wg  := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(i int) {
            if gmlock.TryLock(key) {
                glog.Println(i)
                time.Sleep(time.Second)
                gmlock.Unlock(key)
            } else {
                glog.Println(false)
            }
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```
同理，在该示例中，同时也只有`1`个goroutine能获得锁，其他goroutine在`TryLock`失败便直接退出了。

执行后，输出结果为：
```html
2018-10-16 00:01:59.172 9
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.172 false
2018-10-16 00:01:59.176 false
```

# 示例4，多个锁机制冲突

该示例用来演示在复杂逻辑下的锁机制处理情况。

```go
package main

import (
    "github.com/gogf/gf/g/os/gmlock"
    "time"
    "github.com/gogf/gf/g/os/glog"
    "fmt"
)

// 内存锁 - 手动Unlock与计时Unlock冲突校验
func main() {
    key := "key"

    // 第一次锁带时间
    gmlock.Lock(key, 1000)
    glog.Println("lock1")
    // 这个时候上一次的计时解锁已失效
    gmlock.Unlock(key)
    glog.Println("unlock1")

    fmt.Println()

    // 第二次锁，不带时间，且在执行过程中前一个Lock的定时解锁生效
    gmlock.Lock(key)
    glog.Println("lock2")
    go func() {
        // 正常情况下3秒后才能执行这句
        gmlock.Lock(key)
        glog.Println("lock by goroutine")
    }()
    time.Sleep(3*time.Second)
    // 这时再解锁
    gmlock.Unlock(key)
    // 注意3秒之后才会执行这一句
    glog.Println("unlock2")

    // 阻塞进程
    select{}
}
```

执行后，输出结果为：
```html
2018-10-16 00:03:40.277 lock1
2018-10-16 00:03:40.279 unlock1

2018-10-16 00:03:40.279 lock2
2018-10-16 00:03:43.279 unlock2
2018-10-16 00:03:43.279 lock by goroutine
```

# 示例5，多文件并发写的安全控制

在`glog`模块写日志文件的时候有这么一个核心方法，我们拿来看一下（源代码位于 [/g/os/glog/glog_logger.go](https://github.com/gogf/gf/blob/master/g/os/glog/glog_logger.go)）。

```go
// 这里的写锁保证同一时刻只会写入一行日志，防止串日志的情况
func (l *Logger) print(std io.Writer, s string) {
    // 优先使用自定义的IO输出
    if l.printHeader.Val() {
        s = l.format(s)
    }
    writer := l.GetWriter()
    if writer == nil {
        // 如果设置的writer为空，那么其次判断是否有文件输出设置
        // 内部使用了内存锁，保证在glog中对同一个日志文件的并发写入不会串日志(并发安全)
        if f := l.getFilePointer(); f != nil {
            defer f.Close()
            key := l.path.Val()
            gmlock.Lock(key)
            _, err := io.WriteString(f, s)
            gmlock.Unlock(key)
            if err != nil {
                fmt.Fprintln(os.Stderr, err.Error())
            }
        }
    } else {
        l.doStdLockPrint(writer, s)
    }
    // 是否允许输出到标准输出
    if l.alsoStdPrint.Val() {
        l.doStdLockPrint(std, s)
    }
}
```

其中的：

```go
gmlock.Lock(key)
...
gmlock.Unlock(key)
```
便使用到了内存锁的特性，其中的变量`key`表示的是日志文件的`绝对路径`，当多个goroutine对同一个日志文件进行写入时，由`gmlock.Lock(key)`来保证对该文件的并发安全写操作。