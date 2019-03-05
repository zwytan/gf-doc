
[TOC]

# 基本介绍

`gf`框架的`ORM`模块由`gdb`模块实现（常被称作`gform`），最大的特色在于底层默认使用了```map```作为基础的数据表记录载体，而不是使用```struct```，开发者无需预先定义数据表记录`struct`便可直接对数据表记录执行各种操作。这样的设计赋予了开发者更高的灵活度和简便性，当然ORM也支持`数据表记录`与`struct`的映射转换，详细介绍请查看后续【[ORM高级特性](database/orm/senior.md)】章节。

> `gdb`数据库引擎底层采用了**链接池设计**，当链接不再使用时会自动关闭，因此链接对象不用的时候不需要显示使用`Close`方法关闭数据库连接（并且`gdb`也没有提供Close方法）。这也是`gdb`数据库模块人性化设计的地方，方便开发者使用数据库而无需手动维护大量的数据库链接对象。

**注意：为提高数据库操作安全性，在ORM操作中不建议直接将参数拼接成SQL执行，又或者将参数拼接称字符串执行，建议尽量使用预处理的方式（充分使用```?```占位符）来传递SQL参数。**

接口文档：
https://godoc.org/github.com/gogf/gf/g/database/gdb

# 数据结构

为便于数据表记录的操作，ORM定义了5种基本的数据类型：

```go
type Map         map[string]interface{} // 数据记录
type List        []Map                  // 数据记录列表

type Value       []byte                 // 返回数据表记录值
type Record      map[string]Value       // 返回数据表记录键值对
type Result      []Record               // 返回数据表记录列表
```

1. ```Map```与```List```用于ORM操作过程中的输入参数类型（与全局类型```g.Map```和```g.List```一致，在项目开发中常用`g.Map`和`g.List`替换）；
2. ```Value/Record/Result```用于ORM操作的结果数据类型，其中```Result```表示数据表记录列表，```Record```表示一条数据表记录，```Value```表示记录中的一条键值数据；

## 类型识别

使用`gform`查询数据时，返回的数据类型将会被自动识别映射到`Go变量类型`。例如: 当字段类型为`int(xx)`时，查询到的字段值类型将会被识别会`int`类型；当字段类型为`varchar(xxx)`/`char(xxx)`/`text`等类型时将会被自动识别为`string`类型。以下以`mysql`类型为例，介绍数据库类型与Go变量类型的自动识别映射关系:

|数据库类型 | Go变量类型
|---|---
|`*char`   | `string`
|`*text`   | `string`
|`*binary` | `bytes`
|`*blob`   | `bytes`
|`*int`    | `int`
|`bit`     | `int`
|`big_int` | `int64`
|`float`   | `float64`
|`double`  | `float64`
|`decimal` | `float64`
|`bool`    | `bool`
|`其他`     | `string`

这一特性对于需要将查询结果进行编码，并通过例如`JSON`方式直接返回给客户端来说将会非常友好。

## 类型转换

`gform`的数据记录结果（```Value```）支持非常灵活的类型转换，并内置支持常用的数十种数据类型的转换。```Result```/```Record```的类型转换请查看后续【[ORM高级特性](database/orm/senior.md)】章节。

> `Value`类型是`*gvar.Var`类型的别名，因此可以使用`gvar.Var`数据类型的所有转换方法，具体请查看【[通用动态变量](container/gvar/index.md)】章节

使用示例：

首先，数据表定义如下：
```sql
# 商品表
CREATE TABLE `goods` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(300) NOT NULL COMMENT '商品名称',
  `price` decimal(10,2) NOT NULL COMMENT '商品价格',
  ...
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
其次，数据表中的数据如下：
```html
id   title     price
1    IPhoneX   5999.99
```
最后，完整的示例程序如下：
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g"
    "github.com/gogf/gf/g/os/glog"
)

func main() {
	g.Config().SetPath("/home/john/Workspace/github.com/gogf/gf/geg/frame")
    db := g.Database()
    if r, err := db.Table("goods").Where("id=?", 1).One(); err == nil {
        fmt.Printf("goods    id: %d\n",   r["id"].Int())
        fmt.Printf("goods title: %s\n",   r["title"].String())
        fmt.Printf("goods proce: %.2f\n", r["price"].Float32())
    } else {
        glog.Error(err)
    }
}
```
执行后，输出结果为：
```shell
goods    id: 1
goods title: IPhoneX
goods proce: 5999.99
```

# 使用`g.Database/g.DB`与`gdb.New`的区别

获取数据库操作对象有两种方式，一种是使用`g.Database/g.DB`方法，一种是使用原生`gdb.New`方法，而前面一种是推荐的使用方式。这两种方式的区别如下：
1. 使用`g.*`对象管理方式获取的是单例对象，而`gdb.New`是创建一个新的数据库对象(往往需要自行进行单例管理)；
1. 使用`g.*`整合了配置文件的管理对接(支持配置文件热更新)，而`gdb.New`无法使用配置文件，必须开发者自行解析随后调用`gdb`的配置管理方法进行配置；
1. 其他使用无差别；
