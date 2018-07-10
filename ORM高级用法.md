
[TOC]

>[danger] # Debug调试模式

为便于开发阶段调试，ORM支持调试模式，可以使用以下方式开启调试模式：
```go
func (db *Db) SetDebug(debug bool)
```
随后我们可以通过以下方法获得调试过程中执行的所有SQL语句：
```go
func (db *Db) GetQueriedSqls() []*Sql
```

使用示例：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/database/gdb"
)

var db *gdb.Db

// 初始化配置及创建数据库
func init () {
    gdb.AddDefaultConfigNode(gdb.ConfigNode {
       Host    : "127.0.0.1",
       Port    : "3306",
       User    : "root",
       Pass    : "123456",
       Name    : "test",
       Type    : "mysql",
       Role    : "master",
       Charset : "utf8",
    })
    db, _ = gdb.New()
}


func main() {
    db.SetDebug(true)
    // 执行3条SQL查询
    for i := 1; i <= 3; i++ {
        db.Table("user").Where("uid=?", i).One()
    }
    // 构造一条错误查询
    db.Table("user").Where("no_such_field=?", "just_test").One()

    for k, v := range db.GetQueriedSqls() {
        fmt.Println(k, ":")
        fmt.Println("Sql  :", v.Sql)
        fmt.Println("Args :", v.Args)
        fmt.Println("Error:", v.Error)
        fmt.Println("Cost :", v.Cost)
        fmt.Println("Func :", v.Func)
    }
}
```
执行后，输出结果如下：
```shell
0 :
Sql  : SELECT * FROM user WHERE no_such_field=?
Args : [just_test]
Error: Error 1054: Unknown column 'no_such_field' in 'where clause'
Cost : 0
Func : DB:Query
1 :
Sql  : SELECT * FROM user WHERE uid=?
Args : [3]
Error: <nil>
Cost : 0
Func : DB:Query
2 :
Sql  : SELECT * FROM user WHERE uid=?
Args : [2]
Error: <nil>
Cost : 0
Func : DB:Query
3 :
Sql  : SELECT * FROM user WHERE uid=?
Args : [1]
Error: <nil>
Cost : 3
Func : DB:Query
```
需要注意的是，获取的已执行SQL列表是按照从最新到旧进行排序(最近执行的SQL排在最前面)；输出结果的SQL中如果出现```?```占位符号，表示这条SQL语句是使用的预处理执行(gf-orm底层采用的也是预处理模式，预防注入风险)，SQL的执行参数是存放到```Args```属性中；```Cost```表示这条SQL花费的执行时间，单位为**毫秒**；```Error```表示这条SQL执行是否产生错误；```Func```表示这条SQL使用的是何种方法执行的，总共有四种：DB:Query/DB:Exec/TX:Query/TX:Exec。


>[danger] # Record转Struct对象

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

>[danger] # Result结果集类型转换

```Result```数据类型根据数据结果集操作的需要，往往需要根据记录中**特定的字段**作为键名进行数据检索，因此它也包含多个用于转换的方法，如下：
```go
// 数据表记录
type Record
    func (r Record) ToMap() Map
    func (r Record) ToStruct(obj interface{}) error
```

```go
// 数据表记录集
type Result
    func (r Result) ToList() List
    func (r Result) ToStringMap(key string) map[string]Map
    func (r Result) ToIntMap(key string) map[int]Map
	func (r Result) ToUintMap(key string) map[uint]Map

    func (r Result) ToStringRecord(key string) map[string]Record
    func (r Result) ToIntRecord(key string) map[int]Record
    func (r Result) ToUintRecord(key string) map[uint]Record
```
由于方法比较简单，这里便不再举例说明。需要注意的是两个方法```Record.ToMap```及```Result.ToList```，这两个方法也是使用比较频繁的方法，用以将ORM查询结果信息转换为可做展示的数据类型。例如，由于结果集字段值底层为```[]byte```类型，虽然使用了新的```Value```类型做了封装，并且也提供了17种类型转换方法，但是大多数时候需要直接将结果```Result```或者```Record```直接作为```json```或者```xml```数据结构返回，就需要做转换才行。



