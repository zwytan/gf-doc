
[TOC]

>[danger] # Record与Struct

gf-ORM为数据表记录操作提供了极高的灵活性和简便性，除了支持以map的形式访问/操作数据表记录以外，也支持将数据表记录转换为struct进行处理。我们以下使用一个简单的示例来演示该特性。

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

通过```gdb.Model.One```方法获取的返回数据表记录是一个```gdb.Map```数据类型，我们可以直接通过它的```ToStruct(obj interface{}) error```方法转换为指定的struct对象。

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

>[danger] # Result数据类型转换

```Result```数据类型根据数据结果集操作的需要，往往需要根据记录中**特定的字段**作为键名进行数据检索，因此它也包含多个用于转换的方法，如下：
```go
type Record
    func (r Record) ToMap() Map
    func (r Record) ToStruct(obj interface{}) error
type Result
    func (r Result) ToIntMap(key string) map[int]Map
    func (r Result) ToIntRecord(key string) map[int]Record
    func (r Result) ToList() List
    func (r Result) ToStringMap(key string) map[string]Map
    func (r Result) ToStringRecord(key string) map[string]Record
    func (r Result) ToUintMap(key string) map[uint]Map
    func (r Result) ToUintRecord(key string) map[uint]Record
```

由于方法比较简单，这里便不再举例说明。



