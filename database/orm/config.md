
[TOC]


# 数据结构

```go
type Config      map[string]ConfigGroup // 数据库配置对象
type ConfigGroup []ConfigNode           // 数据库分组配置
// 数据库配置项(一个分组配置对应多个配置项)
type ConfigNode  struct {
    Host     string // 地址
    Port     string // 端口
    User     string // 账号
    Pass     string // 密码
    Name     string // 数据库名称
    Type     string // 数据库类型：mysql, sqlite, mssql, pgsql, oracle(目前仅支持mysql,pgsql)
    Role     string // (可选，默认为master)数据库的角色，用于主从操作分离，至少需要有一个master，参数值：master, slave
    Charset  string // (可选，默认为 utf-8)编码，默认为 utf-8
    Priority int    // (可选)用于负载均衡的权重计算，当集群中只有一个节点时，权重没有任何意义
    Linkinfo string // (可选)自定义链接信息，当该字段被设置值时，以上链接字段(Host,Port,User,Pass,Name)将失效(该字段是一个扩展功能，参考sql.Open参数)
}
```

`ConfigNode`用于存储一个数据库节点信息；```ConfigGroup```用于管理多个数据库节点组成的配置分组(一般一个分组对应一个业务数据库集群)；```Config```用于管理多个ConfigGroup配置分组。

**配置管理特点：**

1. 支持多节点数据库集群管理，采用单例模式管理数据库实例化对象；
1. 支持对数据库集群分组管理，按照分组名称获取实例化的数据库操作对象；
2. 支持多种关系型数据库管理，可通过ConfigNode.Type属性进行配置(目前仅支持mysql和pgsql数据库)；
3. 支持Master-Slave读写分离，可通过ConfigNode.Role属性进行配置；
4. 支持客户端的负载均衡管理，可通过ConfigNode.Priority属性进行配置，值越大，优先级越高；

特别说明，gform的配置管理最大的**特点**是，(同一进程中)所有的数据库集群信息都使用同一个配置管理模块进行统一维护，**不同业务的数据库集群配置使用不同的分组名称**进行配置和获取。


# (推荐)配置文件

> 推荐使用配置文件及单例对象来管理和使用数据库操作。

如果我们使用对象管理包中的```g.Database()/g.DB()```方法获取数据库操作对象(`g.DB()`为`g.Database()`的别名方法)，那么数据库配置可以在全局配置文件`config.toml`中进行配置，配置项的数据格式形如：
```toml
[database]
    [[database.分组名称]]
        host     = "地址"
        port     = "端口"
        user     = "账号"
        pass     = "密码"
        name     = "数据库名称"
        type     = "数据库类型(目前支持mysql/pgsql/sqlite)"
        role     = "数据库主从角色(master/slave)，不使用应用层的主从机制请均设置为master"
        charset  = "数据库编码(如: utf8/gbk/gb2312)，一般设置为utf8"
        priority = "优先级，用于负载均衡控制，不使用应用层的负载均衡机制请均设置为1"
```
一个数据库配置项示例：
```toml
[database]
    [[database.default]]
        host     = "127.0.0.1"
        port     = "3306"
        user     = "root"
        pass     = ""
        name     = "test"
        type     = "mysql"
        role     = "master"
        charset  = "utf8"
        priority = "1"
```

随后，我们可以通过```g.Database("数据库分组名称")/g.DB("数据库分组名称")```来获取一个数据库操作对象，对象管理器会自动读取并解析配置文件中的数据库配置信息，并生成对应的数据库对象，非常简便。

# 配置方法

> 这是原生调用`gdb`模块来配置管理数据库。

数据库配置管理方法列表：
```go
// 添加一个数据库节点到指定的分组中
func AddConfigNode(group string, node ConfigNode)
// 添加一个配置分组到数据库配置管理中(同名覆盖)
func AddConfigGroup(group string, nodes ConfigGroup)

// 添加一个数据库节点到默认的分组中(默认为default，可修改)
func AddDefaultConfigNode(node ConfigNode)
// 添加一个配置分组到数据库配置管理中(默认分组为default，可修改)
func AddDefaultConfigGroup(nodes ConfigGroup)

// 设置默认的分组名称，获取默认数据库对象时将会自动读取该分组配置
func SetDefaultGroup(groupName string)

// 设置数据库配置为定义的配置信息，会将原有配置覆盖
func SetConfig(c Config)
```

默认分组表示，如果获取数据库对象时不指定配置分组名称，那么gdb默认读取的配置分组。例如：```gdb.New()```可获取一个默认分组的数据库对象。

简单的做法，我们可以通过gdb包的```SetConfig```配置管理方法进行自定义的数据库全局配置，例如：
```go
gdb.SetConfig(gdb.Config {
    "default" : gdb.ConfigGroup {
        gdb.ConfigNode {
            Host     : "192.168.1.100",
            Port     : "3306",
            User     : "root",
            Pass     : "123456",
            Name     : "test",
            Type     : "mysql",
            Role     : "master",
            Priority : 100,
        },
        gdb.ConfigNode {
            Host     : "192.168.1.101",
            Port     : "3306",
            User     : "root",
            Pass     : "123456",
            Name     : "test",
            Type     : "mysql",
            Role     : "slave",
            Priority : 100,
        },
    },
    "user-center" : gdb.ConfigGroup {
        gdb.ConfigNode {
            Host     : "192.168.1.110",
            Port     : "3306",
            User     : "root",
            Pass     : "123456",
            Name     : "test",
            Type     : "mysql",
            Role     : "master",
            Priority : 100,
        },
    },
})
```
随后，我们可以使用```gdb.New("数据库分组名称")```来获取一个数据库操作对象。该对象用于后续的数据库一系列方法/链式操作。



