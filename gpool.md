>[danger] # gpool

对象复用池。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gpool"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gpool

```go
type Pool
    func New(expire int, newFunc ...func() (interface{}, error)) *Pool
    func (p *Pool) Close()
    func (p *Pool) Get() (interface{}, error)
    func (p *Pool) Put(value interface{})
    func (p *Pool) SetExpireFunc(expireFunc func(interface{}))
    func (p *Pool) Size() int
```

文档未完待续。