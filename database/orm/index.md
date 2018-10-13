
[TOC]

# 基本介绍

gf框架的ORM模块由`gdb`模块实现([API文档](https://godoc.org/github.com/johng-cn/gf/g/database/gdb))，最大的特色在于底层默认使用了```map```作为基础的数据表记录载体，而不是使用```struct```，开发者无需预先定义数据表记录`struct`便可直接对数据表记录执行各种操作。这样的设计赋予了开发者更高的灵活度和简便性，并且由于没有使用反射特性，使得执行性能更加高效（当然ORM也支持`数据表记录`与`struct`的映射转换，详细介绍请查看后续【[ORM高级特性](database/orm/senior.md)】章节）。

> `gdb`数据库引擎底层采用了**链接池设计**，当链接不再使用时会自动关闭，因此链接对象不用的时候不需要显示使用`Close`方法关闭数据库连接（并且`gdb`也没有提供Close方法）。这也是`gdb`数据库模块人性化设计的地方，方便开发者使用数据库而无需手动维护大量的数据库链接对象。

**注意：为提高数据库操作安全性，在ORM操作中不建议直接将参数拼接成SQL执行，又或者将参数拼接称字符串执行，建议尽量使用预处理的方式（充分使用```?```占位符）来传递SQL参数。**



## 数据结构

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



## 类型转换

`gf-orm`的数据记录结果（```Value```）支持非常灵活的类型转换，并内置支持常用的数十种数据类型的转换。```Result```/```Record```的类型转换请查看后续【[ORM高级特性](database/orm/senior.md)】章节。

> `Value`类型是`*gvar.Var`类型的别名，因此可以使用`gvar.Var`数据类型的所有转换方法，具体请查看【[通用动态变量](container/gvar/index.md)】章节

### 示例1，基本使用

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
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/os/glog"
)

func main() {
	g.Config().SetPath("/home/john/Workspace/gitee.com/johng/gf/geg/frame")
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
