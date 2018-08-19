
# gcache

gcache是一个高速的单进程缓存模块，提供了并发安全的缓存控制接口。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gcache"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/os/gcache](https://godoc.org/github.com/johng-cn/gf/g/os/gcache)
```go
func BatchRemove(keys []string)
func BatchSet(data map[string]interface{}, expire int64)
func Get(key string) interface{}
func Keys() []string
func Lock(key string, expire int64) bool
func Remove(key string)
func Set(key string, value interface{}, expire int64)
func SetCap(cap int)
func Size() int
func Unlock(key string)
func Values() []interface{}
type Cache
    func New() *Cache
    func (c *Cache) BatchRemove(keys []string)
    func (c *Cache) BatchSet(data map[string]interface{}, expire int64)
    func (c *Cache) Close()
    func (c *Cache) Get(key string) interface{}
    func (c *Cache) Keys() []string
    func (c *Cache) Lock(key string, expire int64) bool
    func (c *Cache) Remove(key string)
    func (c *Cache) Set(key string, value interface{}, expire int64)
    func (c *Cache) SetCap(cap int)
    func (c *Cache) Size() int
    func (c *Cache) Unlock(key string)
    func (c *Cache) Values() []interface{}

```

