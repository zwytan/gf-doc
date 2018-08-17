# glist

并发安全双向列表。

使用方式：
```go
import "gitee.com/johng/gf/g/container/glist"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/glist
```go
type SafeList
    func NewSafeList() *SafeList
    func (this *SafeList) Back() *list.Element
    func (this *SafeList) BackAll() []interface{}
    func (this *SafeList) BackItem() interface{}
    func (this *SafeList) BatchPopBack(max int) []interface{}
    func (this *SafeList) BatchPopFront(max int) []interface{}
    func (this *SafeList) BatchPushFront(vs []interface{})
    func (this *SafeList) Front() *list.Element
    func (this *SafeList) FrontAll() []interface{}
    func (this *SafeList) FrontItem() interface{}
    func (this *SafeList) InsertAfter(v interface{}, mark *list.Element) *list.Element
    func (this *SafeList) InsertBefore(v interface{}, mark *list.Element) *list.Element
    func (this *SafeList) Len() int
    func (this *SafeList) PopBack() interface{}
    func (this *SafeList) PopBackAll() []interface{}
    func (this *SafeList) PopFront() interface{}
    func (this *SafeList) PopFrontAll() []interface{}
    func (this *SafeList) PushBack(v interface{}) *list.Element
    func (this *SafeList) PushFront(v interface{}) *list.Element
    func (this *SafeList) Remove(e *list.Element) interface{}
    func (this *SafeList) RemoveAll()
```

文档未完待续。