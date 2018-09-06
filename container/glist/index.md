# glist

并发安全双向列表。

使用场景：

并发安全场景下的链表操作，也可以关闭并发安全性当做普通的链表来使用。

使用方式：
```go
import "gitee.com/johng/gf/g/container/glist"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/container/glist](https://godoc.org/github.com/johng-cn/gf/g/container/glist)
```go
type SafeList
    func New(safe ...bool) *List
    func (this *List) Back() *list.Element
    func (this *List) BackAll() []interface{}
    func (this *List) BackItem() interface{}
    func (this *List) BatchPopBack(max int) []interface{}
    func (this *List) BatchPopFront(max int) []interface{}
    func (this *List) BatchPushFront(vs []interface{})
    func (this *List) Front() *list.Element
    func (this *List) FrontAll() []interface{}
    func (this *List) FrontItem() interface{}
    func (this *List) InsertAfter(v interface{}, mark *list.Element) *list.Element
    func (this *List) InsertBefore(v interface{}, mark *list.Element) *list.Element
    func (this *List) Len() int
    func (this *List) PopBack() interface{}
    func (this *List) PopBackAll() []interface{}
    func (this *List) PopFront() interface{}
    func (this *List) PopFrontAll() []interface{}
    func (this *List) PushBack(v interface{}) *list.Element
    func (this *List) PushFront(v interface{}) *list.Element
    func (this *List) Remove(e *list.Element) interface{}
    func (this *List) RemoveAll()
```

由于链表本身是比较简单的数据结构，这里便不再举例说明，通过方法名称即可知其意。
