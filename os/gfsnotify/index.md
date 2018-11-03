[TOC]

`gfsnotify`能监控指定文件/目录的改变，如文件的增加、删除、修改、重命名等操作。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gfsnotify"
```

# 方法列表
[godoc.org/github.com/johng-cn/gf/g/os/gfsnotify](https://godoc.org/github.com/johng-cn/gf/g/os/gfsnotify)
```go
func Add(path string, callback func(event *Event)) error
func Remove(path string) error

type Event
    func (e *Event) String() string
    func (e *Event) IsChmod() bool
    func (e *Event) IsCreate() bool
    func (e *Event) IsRemove() bool
    func (e *Event) IsRename() bool
    func (e *Event) IsWrite() bool

type Watcher
    func New() (*Watcher, error)
    func (w *Watcher) Add(path string, callback func(event *Event)) error
    func (w *Watcher) Close()
    func (w *Watcher) Remove(path string) error
```

推荐使用`gfsnotify`模块提供的```Add```和```Remove```模块方法，用于添加监控和取消监控。推荐原因见随后章节说明。

此外也可能通过```New```方法创建一个监控管理对象之后再进行监控管理。其中，添加监控的时候需要给定触发监控时的回调函数，参数类型为```*gfsnotify.Event```对象指针。


# 使用示例
```go
package main

import (
    "gitee.com/johng/gf/g/os/gfsnotify"
    "gitee.com/johng/gf/g/os/glog"
)

func main() {
    err := gfsnotify.Add("/home/john", func(event *gfsnotify.Event) {
        if event.IsCreate() {
            glog.Println("创建文件 : ", event.Path)
        }
        if event.IsWrite() {
            glog.Println("写入文件 : ", event.Path)
        }
        if event.IsRemove() {
            glog.Println("删除文件 : ", event.Path)
        }
        if event.IsRename() {
            glog.Println("重命名文件 : ", event.Path)
        }
        if event.IsChmod() {
            glog.Println("修改权限 : ", event.Path)
        }
        glog.Println(event)
    })
    if err != nil {
        glog.Fatalln(err)
    } else {
        select {}
    }
}
```
其中`/home/john`参数为一个目录，`gfsnotify.Add`方法默认为递归监控，也就是说当目录下的文件(包括子目录下的文件)发生变化时，也会收到文件监控信息回调。

当我们在```/home/john```目录下创建/删除/修改文件时，可以看到`gfsnotify`监控到了文件的修改并输出了对应的事件信息。


# `fs.inotify.max_user_instances`与`fs.inotify.max_user_watches`

在`*nix`系统下，`gfsnotify`模块使用的是系统的`inotify`特性来实现的文件/目录监控，因此该功能在使用时会受到系统的两个内核函数限制：

- `fs.inotify.max_user_instances`：表示当前用户可创建的`inotify`监控实例数量，即`gfsnotify.New`方法创建的`Watcher`对象数量，一个`Watcher`对象对应系统的一个`inotify`实例，系统默认数量为：`128`；

- `fs.inotify.max_user_watches`：表示一个`inotify`实例可添加的监控文件队列大小，往同一个`inotify`添加的监控文件超过该数量限制则会失败，并且会有系统错误日志，系统默认数量往往为：`8192`(有的系统该数值会比较大一些)；

我们推荐使用`gfsnotify`的包方法`Add`/`Remove`来操作文件监控，在`gfsnotify`底层实现中会默认创建多个(默认为`4`个)`Watcher`实例来管理文件监控。不同文件路径会被按照一定规则自动地分配到不同的`Watcher`实例中进行管理，开发者不必关心是否达到文件监控上限问题，并且也能避免可能产生的`fs.inotify.max_user_instances`上限问题。