# glist

并发安全双向列表。

**使用场景**：

并发安全场景下的链表操作，也可以关闭并发安全性当做普通的链表来使用。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/glist"
```

**接口文档**：[godoc.org/github.com/gogf/gf/g/container/glist](https://godoc.org/github.com/gogf/gf/g/container/glist)
```go
type SafeList
    func New(unsafe ...bool) *List
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

## 性能基准测试

```
goos: darwin
goarch: amd64
pkg: gitee.com/johng/gf/g/container/glist
Benchmark_PushBack-4    	20000000	       194 ns/op
Benchmark_PushFront-4   	20000000	       243 ns/op
Benchmark_Len-4         	20000000	        20.3 ns/op
Benchmark_PopFront-4    	20000000	        65.6 ns/op
Benchmark_PopBack-4     	20000000	        51.8 ns/op
PASS
```
