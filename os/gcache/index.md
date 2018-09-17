[TOC]


# gcache

`gcache`是一个高速的单进程缓存模块，提供了并发安全的缓存控制接口。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gcache"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/os/gcache](https://godoc.org/github.com/johng-cn/gf/g/os/gcache)
```go
func BatchRemove(keys []interface{})
func BatchSet(data map[interface{}]interface{}, expire int)
func Contains(key interface{}) bool
func Get(key interface{}) interface{}
func GetOrSet(key interface{}, value interface{}, expire int) interface{}
func GetOrSetFunc(key interface{}, f func() interface{}, expire int) interface{}
func KeyStrings() []string
func Keys() []interface{}
func Remove(key interface{})
func Set(key interface{}, value interface{}, expire int)
func SetCap(cap int)
func SetIfNotExist(key interface{}, value interface{}, expire int) bool
func Size() int
func Values() []interface{}
type Cache
    func New() *Cache
    func (c *Cache) BatchRemove(keys []interface{})
    func (c *Cache) BatchSet(data map[interface{}]interface{}, expire int)
    func (c *Cache) Clear()
    func (c *Cache) Close()
    func (c *Cache) Contains(key interface{}) bool
    func (c *Cache) Get(key interface{}) interface{}
    func (c *Cache) GetOrSet(key interface{}, value interface{}, expire int) interface{}
    func (c *Cache) GetOrSetFunc(key interface{}, f func() interface{}, expire int) interface{}
    func (c *Cache) KeyStrings() []string
    func (c *Cache) Keys() []interface{}
    func (c *Cache) Remove(key interface{})
    func (c *Cache) Set(key interface{}, value interface{}, expire int)
    func (c *Cache) SetCap(cap int)
    func (c *Cache) SetIfNotExist(key interface{}, value interface{}, expire int) bool
    func (c *Cache) Size() int
    func (c *Cache) Values() []interface{}
```
`gcache`可以使用`New`方法创建使用，并且也可以使用包方法使用。在通过包方法使用缓存功能时，其实操作`gcache`默认提供的一个`gcache.Cache`对象，具有全局性，因此在使用时注意全局键名的覆盖。

`gcache`比较有特色的地方是键名使用的是`interface{}`类型，而不是`string`类型，这意味着我们可以使用任意类型的变量作为键名，但大多数时候建议使用`string`或者`[]byte`作为键名，并且统一键名的数据类型，以便维护。

另外需要注意的是，`gcache`的缓存时间单位为`毫秒`，在`Set`缓存变量时，缓存时间参数`expire=0`表示不过期，`expire<0`表示立即过期，`expire>0`表示超时过期。

## 示例1，基本使用

```go
package main

import (
    "gitee.com/johng/gf/g/os/gcache"
    "gitee.com/johng/gf/g"
)

func main() {
    // 创建一个缓存对象，当然也可以直接使用gcache包方法
    c := gcache.New()

    // 设置缓存，不过期
    c.Set("k1", "v1", 0)

    // 获取缓存
    g.Dump(c.Get("k1"))

    // 缓存中是否存在指定键名
    g.Dump(c.Contains("k1"))

    // 删除并返回被删除的键值
    g.Dump(c.Remove("k1"))

    // 关闭缓存对象，让GC回收资源
    c.Close()
}
```

执行后，输出结果为：

```html
"v1"
1
true
"v1"
```


## 示例2，缓存控制

```go
package main

import (
    "gitee.com/johng/gf/g/os/gcache"
    "gitee.com/johng/gf/g"
    "time"
)

func main() {
    // 当键名不存在时写入，设置过期时间1000毫秒
    gcache.SetIfNotExist("k1", "k1", 1000)

    // 打印当前的键名列表
    g.Dump(gcache.Keys())

    // 打印当前的键名列表 []string 类型
    g.Dump(gcache.KeyStrings())

    // 获取指定键值，如果不存在时写入，并返回键值
    g.Dump(gcache.GetOrSet("k2", "v2", 0))

    // 打印当前的键值列表
    g.Dump(gcache.Values())

    // 等待1秒，以便k1:v1自动过期
    time.Sleep(time.Second)

    // 再次打印当前的键值列表，发现k1:v1已经过期，只剩下k2:v2
    g.Dump(gcache.Values())
}
```

执行后，输出结果为：

```html
[
	"k1"
]
[
	"k1"
]
"v2"
[
	"k1",
	"v2"
]
[
	"v2"
]
```

## 示例3，GetOrSetFunc

`GetOrSetFunc`获取一个缓存值，当缓存不存在时执行指定的`func() interface{}`，缓存该`func`的结果值，并返回该结果值。
我们来看一个在`gf-home`项目中使用的示例，该示例遍历检索`markdown`文件进行字符串检索，并根据指定的搜索`key`缓存该结果值，因此多次搜索该`key`时，第一次执行目录遍历搜索，后续将直接使用缓存。

[gitee.com/johng/gf-home/blob/master/app/lib/doc/doc.go](https://gitee.com/johng/gf-home/blob/master/app/lib/doc/doc.go)

```go
// 根据关键字进行markdown文档搜索，返回文档path列表
func SearchMdByKey(key string) []string {
    glog.Cat("search").Println(key)
    v := cache.GetOrSetFunc("doc_search_result_" + key, func() interface{} {
        // 当该key的检索缓存不存在时，执行检索
        array    := garray.NewStringArray(0, 0, false)
        docPath  := g.Config().GetString("doc.path")
        paths    := cache.GetOrSetFunc("doc_files_recursive", func() interface{} {
            // 当目录列表不存在时，执行检索
            paths, _ := gfile.ScanDir(docPath, "*.md", true)
            return paths
        }, 0)
        // 遍历markdown文件列表，执行字符串搜索
        for _, path := range gconv.Strings(paths) {
            content := gfcache.GetContents(path)
            if len(content) > 0 {
                if strings.Index(content, key) != -1 {
                    index := gstr.Replace(path, ".md", "")
                    index  = gstr.Replace(index, docPath, "")
                    array.Append(index)
                }
            }
        }
        return array.Slice()
    }, 0)

    return gconv.Strings(v)
}
```

## 示例4，LRU缓存淘汰控制

未完待续。