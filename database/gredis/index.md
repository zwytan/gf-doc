# gredis

Redis客户端由```gredis```模块实现，底层采用了**链接池设计**，开发者无法手动`Close`链接对象。

使用方式：
```go
import "gitee.com/johng/gf/g/database/gredis"
```

方法列表：[godoc.org/github.com/gogf/gf/g/database/gredis](https://godoc.org/github.com/gogf/gf/g/database/gredis)
```go
func New(address string, db ...interface{}) *Redis
func (r *Redis) Close() error

func (r *Redis) Do(command string, args ...interface{}) (interface{}, error)
func (r *Redis) Send(command string, args ...interface{}) error

func (r *Redis) SetIdleTimeout(value time.Duration)
func (r *Redis) SetMaxActive(value int)
func (r *Redis) SetMaxConnLifetime(value time.Duration)
func (r *Redis) SetMaxIdle(value int)

func (r *Redis) Stats() *PoolStats
```
`gredis`使用了连接池来进行`Redis`对象管理，通过```Set*```方法可以对连接池的属性进行管理，通过```Stats```方法可以获取连接池的统计信息。我们最常用的方法是```Do```和```Send```方法，分别是同步和异步指令，通过向Redis Server发送对应的Redis API命令，来使用Redis Server的服务。

需要注意的是，`Close`方法是关闭链接池，而不是关闭当前的redis操作链接，只有在开发者期望自行维护`gredis`对象的时候才可能涉及到`Close`方法的使用。绝大部分情况下推荐使用`g.Redis`单例方式来操作redis。

- Redis中文手册请参考：http://redisdoc.com/ 
- Redis官方命令请参考：https://redis.io/commands

以下是一个简单的使用gredis的示例：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g/database/gredis"
)

// 使用原生gredis.New操作redis，但是注意需要自己调用Close方法关闭redis链接池
func main() {
    redis := gredis.New(gredis.Config{
        Host : "127.0.0.1",
        Port : 6379,
    })
    defer redis.Close()
    redis.Do("SET", "k", "v")
    v, _ := redis.Do("GET", "k")
    fmt.Println(gconv.String(v))
}
```
该示例中，我们通过```New```方法创建一个Redis操作对象，并通过```Do```方法使用Redis Server的KV功能，随后我们再获取设置的信息。由于Do接口返回的都是```interface{}```类型的返回值，我们这里通过```gconv```模块将任何类型转换为string来进行显示。
执行后输出结果为：
```html
v
```

# 全局配置

> 推荐使用`g.Redis`的方式来获取单例的`gredis`对象，并且由于`gredis`底层采用链接池的方式来管理客户端链接，因此开发者无须手动调用`Close`方式关闭链接对象（并且`gredis`没有提供关闭链接对象的`Close`方法，仅有直接关闭**链接池**的`Close`方法，绝大部分情况下开发者无须关系底层redis链接池的状态维护）。

当然，像Redis这么常用的服务，也已经被对象管理器(```g.*```)进行了封装，其配置也可以通过配置文件进行管理，在```config.toml```中的配置示例如下：
```toml
# Redis数据库配置
[redis]
    default = "127.0.0.1:6379,0"
    cache   = "127.0.0.1:6379,1"
```
其中示例中的```disk```和```cache```分别表示配置分组名称，我们在程序中可以通过该名称获取对应配置的redis对象。redis配置项格式为：```host:port[,db[,pass]]```（即：```地址:端口[,数据库DB[,密码]]```），其中```db```（默认为```0```）及```pass```（默认为空）配置字段为非必须。随后我们可以通过```g.Redis("分组名称")```(不传递分组名称是，默认使用`redis.default`配置分组项)来获取对应配置的redis客户端单例对象。

示例如下：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/util/gconv"
)

// 使用框架封装的g.Redis()方法获得redis操作对象单例，不需要开发者显示调用Close方法
func main() {
    g.Redis().Do("SET", "k", "v")
    v, _ := g.Redis().Do("GET", "k")
    fmt.Println(gconv.String(v))
}
```

执行后，输出结果为：
```html
v
```

