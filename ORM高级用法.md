
[TOC]

gf框架的ORM最大的特色在于底层使用了```gdb.Map```(即```map[string]interface{}```)作为数据表记录载体，而不是使用struct对象，开发者无需预先定义数据表记录struct便可直接对数据表记录执行各种操作。这样的设计赋予了开发者更高的灵活度和简便性，并且由于没有使用反射特性，使得执行性能更加高效。

当然，我们的ORM也支持将记录转换为指定的struct。



>[danger] # 表记录与Struct

首先，我们的用户表结构是这样的（简单设计的示例表）：
```sql
CREATE TABLE `user` (
  `uid` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(30) NOT NULL DEFAULT '' COMMENT '昵称',
  `site` varchar(255) NOT NULL DEFAULT '' COMMENT '主页',
  PRIMARY KEY (`uid`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
其次，我们的表数据如下：
```html
uid  name   site
1    john   http://johng.cn
```
最后，我们的示例程序如下：

```go
package main

import (
    "fmt"
)

type User struct {
    Uid  int
    Name string
}

func main() {
    if r, err := db.Table("user").Where("uid=?", 1).One(); err == nil {
        u := User{}
        if err := r.ToStruct(&u); err == nil {
            fmt.Println(" uid:", u.Uid)
            fmt.Println("name:", u.Name)
        } else {
            fmt.Println(err)
        }
    } else {
        fmt.Println(err)
    }
}
```
执行后，输出结果为：
```shell
 uid: 1
name: john
```
这里，我们自定义了一个struct，里面只包含了```Uid```和```Name```属性，可以看到它的属性并不和数据表的字段一致，这也是ORM灵活的特性之一：支持指定属性获取。

通过```gdb.Model.One```方法获取的返回数据表记录是一个```gdb.Map```数据类型，我们可以直接通过它的```func (m Map) ToStruct(obj interface{}) error```方法转换为指定的struct对象。

需要注意的是，map中的键名为uid,name,site，而struct中的属性为Uid,Name，那么他们之间是如何执行映射的呢？主要是以下几点重要的规则：
1. struct中的属性必须为公开属性；
2. map中的键名会自动将首字母进行大写转换以进行属性匹配；
3. 如果匹配成功，那么将键值赋值给属性，如果无法匹配，那么忽略；
以下是几个匹配的示例：
```html
map键名    struct属性     是否匹配
name       Name           match
Email      Email          match
nickname   Nickname       match
Nick-Name  Nickname       not match
nick_name  Nickname       not match
nick_name  Nick_name      match
```

>[danger] # 表记录列表转换

在ORM中还有一种数据类型为```List```，它的定义为;
```go
// 关联数组列表(索引从0开始的数组)，绑定多条记录
type List []Map
```

即多个表记录列表项。它也包含多个转换的方法，如下：
```go
func (l List) ToStringMap(key string) map[string]Map 
func (l List) ToIntMap(key string) map[int]Map 
func (l List) ToUintMap(key string) map[uint]Map 
```

可以看到，ORM支持将列表数据按照键名转换为各种map类型，便于数据检索操作。

由于方法比较简单，这里便不再举例说明。



