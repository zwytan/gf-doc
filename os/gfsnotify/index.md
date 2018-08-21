
# gfsnotify

`gfsnotify`能监控指定文件的修改情况，如文件的增加、删除、修改、重命名等操作。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gfsnotify"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/os/gfsnotify](https://godoc.org/github.com/johng-cn/gf/g/os/gfsnotify)
```go
func Add(path string, callback func(event *Event)) error
func Remove(path string) error

type Event
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

`gfsnotify`包提供了直接的```Add```和```Remove```包方法，用于添加监控和取消监控。此外也可能通过```New```方法创建一个监控管理对象之后再进行监控管理。其中，添加监控的时候需要给定触发监控时的回调函数，参数为```*gfsnotify.Event```对象指针。

使用示例：
```go
package main

import (
    "log"
    "gitee.com/johng/gf/g/os/gfsnotify"
)

func main() {
    err := gfsnotify.Add("/home/john/Documents/temp", func(event *gfsnotify.Event) {
        if event.IsCreate() {
            log.Println("创建文件 : ", event.Path)
        }
        if event.IsWrite() {
            log.Println("写入文件 : ", event.Path)
        }
        if event.IsRemove() {
            log.Println("删除文件 : ", event.Path)
        }
        if event.IsRename() {
            log.Println("重命名文件 : ", event.Path)
        }
        if event.IsChmod() {
            log.Println("修改权限 : ", event.Path)
        }
    })
    if err != nil {
        log.Fatalln(err)
    } else {
        select {}
    }
}
```
当我们修改```/home/john/Documents/temp```文件时，可以看到gfsnotify监控到了文件的修改并输出了对应的事件信息。
