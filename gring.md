>[danger] # gring

并发安全环结构。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gring"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gring

```go
type Ring
    func New(cap int) *Ring
    func (r *Ring) Cap() int
    func (r *Ring) Len() int
    func (r *Ring) Link(s *Ring) *Ring
    func (r *Ring) LockIteratorNext(f func(item *ring.Ring) bool)
    func (r *Ring) LockIteratorPrev(f func(item *ring.Ring) bool)
    func (r *Ring) Move(n int) *Ring
    func (r *Ring) Next() *Ring
    func (r *Ring) Prev() *Ring
    func (r *Ring) Put(value interface{}) *Ring
    func (r *Ring) RLockIteratorNext(f func(value interface{}) bool)
    func (r *Ring) RLockIteratorPrev(f func(value interface{}) bool)
    func (r *Ring) Set(value interface{}) *Ring
    func (r *Ring) SliceNext() []interface{}
    func (r *Ring) SlicePrev() []interface{}
    func (r *Ring) Unlink(n int) *Ring
    func (r *Ring) Val() interface{}
```


文档未完待续。