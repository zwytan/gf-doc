>[danger] # gredis

Redis客户端由```gredis包```支持，相关方法如下：
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
gredis使用了连接池来进行Redis对象管理，通过```Set*```方法可以对连接池的属性进行管理，通过```Stats```方法可以获取连接池的统计信息。我们最长用的方法是```Do```和```Send```方法，分别是同步和异步指令，通过向Redis Server发送对应的Redis API命令，来使用Redis Server的服务。

Redis中文手册请参考：http://redisdoc.com/ 
Redis官方命令请参考：https://redis.io/commands

以下是一个简单的使用gredis的示例：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g/database/gredis"
)

func main() {
    redis := gredis.New("127.0.0.1:6379", 1)
    redis.Do("SET", "k1", "v1")
    redis.Do("SET", "k2", "v2")
    v1, _ := redis.Do("GET", "k1")
    v2, _ := redis.Do("GET", "k1")
    fmt.Println(gconv.String(v1))
    fmt.Println(gconv.String(v2))
}
```
该示例中，我们通过```New```方法创建一个Redis操作对象，并通过```Do```方法使用Redis Server的KV功能，随后我们再获取设置的信息。由于Do接口返回的都是```interface{}```类型的返回值，我们这里通过```gconv```包将任何类型转换为string来进行显示。
执行后输出结果为：
```html
v1
v2
```

>[danger] # gins.Redis

当然，像Redis这么常用的服务，gins包也是做了当做框架核心对象做了单例封装的，因此，redis的也可以通过配置文件进行管理，在```config.yml```中的配置示例如下：
```yml
# Redis数据库配置
redis:
    disk:  127.0.0.1:6379,0
    cache: 127.0.0.1:6379,1
```
其中示例中的```disk```和```cache```分别表示配置分组名称，我们在程序中可以通过该名称获取对应配置的redis单例对象。redis配置项格式为：```ip:port,db```。随后我们可以通过```gins.Redis("分组名称")```来获取对应配置的redis客户端单例对象。

使用单例管理器的示例：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/frame/gins"
    "gitee.com/johng/gf/g/util/gconv"
)

func main() {
    gins.Config().SetPath("/home/john/Workspace/gitee.com/johng/gf/geg/frame")
    redis := gins.Redis("cache")
    redis.Do("SET", "k", "v")
    v, _ := redis.Do("GET", "k")
    fmt.Println(gconv.String(v))
}
```
为了演示的需要，我们的配置文件放在了```/home/john/Workspace/gitee.com/johng/gf/geg/frame```目录，因此我们在示例中先设置了默认配置管理器的目录。随后我们通过```gins.Redis("cache")```获取分组名称为```cache```的redis配置的单例对象，该分组名称对应的配置为```127.0.0.1:6379,1```。

执行后，输出结果为：
```html
v
```

