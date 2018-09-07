[TOC]

# gpool

对象复用池。将对象进行缓存复用，支持`过期时间`、`创建方法`及`销毁方法`定义。


使用场景：

任何需要对象(struct)复用的场景。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gpool"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/container/gpool](https://godoc.org/github.com/johng-cn/gf/g/container/gpool)

```go
type Pool
    func New(expire int, newFunc ...func() (interface{}, error)) *Pool
    func (p *Pool) Close()
    func (p *Pool) Get() (interface{}, error)
    func (p *Pool) Put(value interface{})
    func (p *Pool) SetExpireFunc(expireFunc func(interface{}))
    func (p *Pool) Size() int
```
需要注意两点：
1. `New`方法的过期时间单位为`毫秒`；
1. 对象`创建方法`(`newFunc ...func() (interface{}, error)`)返回值包含一个`error`返回，当对象创建失败时可由该返回值反馈原因；


## gpool与sync.Pool

`gpool`与`sync.Pool`的作用都是对象复用，但是`sync.Pool`的对象生命周期不支持开发者定义，因此也不确定，并且`sync.Pool`不支持对象创建方法及销毁方法。因此，`gpool`的功能更强大，并且更接近实战一些。

## 使用示例1，基本使用

```go
package main

import (
    "gitee.com/johng/gf/g/container/gpool"
    "fmt"
    "time"
)

func main () {
    // 创建一个对象池，过期时间为1000毫秒
    p := gpool.New(1000)

    // 从池中取一个对象，返回nil及错误信息
    fmt.Println(p.Get())

    // 丢一个对象到池中
    p.Put(1)

    // 重新从池中取一个对象，返回1
    fmt.Println(p.Get())

    // 等待1秒后重试，发现对象已过期，返回nil及错误信息
    time.Sleep(time.Second)
    fmt.Println(p.Get())
}
```


## 使用示例2，文件指针池

这个示例稍微复杂一些，但是却将`gpool`对象池的功能用得很完美。

该示例是一个gf框架的`gfilepool`文件指针池的实现源码。文件指针池和数据库连接池比较类似，是基于IO复用的一种设计。

一般的文件操作过程是这样的：当文件被打开之后，会创建一个文件指针，在文件操作结束后，文件指针就会被立即关闭掉。这样的话，在每一次操作或者请求中，对文件的操作都会反复创建/关闭指针，这样产生的问题：一个是操作效率比较低，另一个是系统的最大文件打开数是有限的，在文件操作频繁/访问量大的情况下，会影响整体系统的执行效率（文件锁，资源竞争），甚至引发一些严重的问题。

文件指针池对于同一个文件在异步高并发下的读取性能提升非常大（因为读取不需要锁，每一个协程一个文件指针），但是对于写入性能的提升不大，因为文件写入往往都必须加锁以保证数据写入的顺序性。

```go
package gfilepool

import (
    "os"
    "sync"
    "gitee.com/johng/gf/g/container/gmap"
    "gitee.com/johng/gf/g/container/gpool"
    "fmt"
)

// 文件指针池
type Pool struct {
    pool    *gpool.Pool     // 底层对象池
}

// 文件指针池指针
type File struct {
    *os.File                // 底层文件指针
    mu     sync.RWMutex     // 互斥锁
    pool   *Pool            // 所属池
}

// 全局指针池，expire < 0表示不过期，expire = 0表示使用完立即回收，expire > 0表示超时回收
var pools = gmap.NewStringInterfaceMap()

// 获得文件对象，并自动创建指针池
func OpenWithPool(path string, flag int, perm os.FileMode, expire int) (*File, error) {
    key    := fmt.Sprintf("%s&%d&%d&%d", path, flag, expire, perm)
    result := pools.Get(key)
    if result != nil {
        return result.(*Pool).File()
    }
    pool := New(path, flag, perm, expire)
    pools.Set(key, pool)
    return pool.File()
}

// 创建一个文件指针池，expire = 0表示不过期，expire < 0表示使用完立即回收，expire > 0表示超时回收
func New(path string, flag int, perm os.FileMode, expire int) *Pool {
    p     := &Pool {}
    p.pool = gpool.New(expire, func() (interface{}, error) {
        file, err := os.OpenFile(path, flag, perm)
        if err != nil {
            return nil, err
        }
        return &File{
            File : file,
            pool : p,
        }, nil
    })
    p.pool.SetExpireFunc(func(i interface{}) {
        i.(*File).File.Close()
    })
    return p
}

// 获得一个文件打开指针
func (p *Pool) File() (*File, error) {
    if v, err := p.pool.Get(); err != nil {
        return nil, err
    } else {
        return v.(*File), nil
    }
}

// 关闭指针池(返回error是标准库io.ReadWriteCloser接口实现)
func (p *Pool) Close() error {
    p.pool.Close()
    return nil
}

// 获得底层文件指针(返回error是标准库io.ReadWriteCloser接口实现)
func (f *File) Close() error {
    f.pool.pool.Put(f)
    return nil
}
```



