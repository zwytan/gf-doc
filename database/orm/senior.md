
[TOC]

# 调试模式

为便于开发阶段调试，gf-orm支持调试模式，可以使用以下方式开启调试模式：
```go
// 是否开启调试服务
func (db *Db) SetDebug(debug bool)
```
随后在ORM的操作过程中，所有的执行语句将会打印到终端进行展示。
同时，我们可以通过以下方法获得调试过程中执行的所有SQL语句：
```go
// 获取已经执行的SQL列表
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
}
```
执行后，输出结果如下：
```shell
2018-08-31 13:54:32.913 [DEBU] SELECT * FROM user WHERE uid=?, [1], 2018-08-31 13:54:32.912, 2018-08-31 13:54:32.913, 1 ms, DB:Query
2018-08-31 13:54:32.915 [DEBU] SELECT * FROM user WHERE uid=?, [2], 2018-08-31 13:54:32.914, 2018-08-31 13:54:32.915, 1 ms, DB:Query
2018-08-31 13:54:32.915 [DEBU] SELECT * FROM user WHERE uid=?, [3], 2018-08-31 13:54:32.915, 2018-08-31 13:54:32.915, 0 ms, DB:Query
2018-08-31 13:54:32.915 [ERRO] SELECT * FROM user WHERE no_such_field=?, [just_test], 2018-08-31 13:54:32.915, 2018-08-31 13:54:32.915, 0 ms, DB:Query
Error: Error 1054: Unknown column 'no_such_field' in 'where clause'
1.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_base.go:120
2.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_base.go:174
3.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_model.go:378
4.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_model.go:301
5.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_model.go:306
6.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/database/gdb/gdb_model.go:311
7.	/home/john/Workspace/Go/GOPATH/src/gitee.com/johng/gf/geg/database/orm/mysql/gdb_debug.go:30
```

# 查询缓存

`gf-orm`支持对查询结果的缓存处理，并支持手动的缓存清理，一切都由业务层调用端自主决定。需要注意的是，查询缓存仅支持链式操作，且在事务操作下不可用。
相关方法：
```go
// 查询缓存/清除缓存操作，需要注意的是，事务查询不支持缓存。
// 当time < 0时表示清除缓存， time=0时表示不过期, time > 0时表示过期时间，time过期时间单位：秒；
// name表示自定义的缓存名称，便于业务层精准定位缓存项(如果业务层需要手动清理时，必须指定缓存名称)，
// 例如：查询缓存时设置名称，清理缓存时可以给定清理的缓存名称进行精准清理。
func (md *Model) Cache(time int, name ... string) *Model
```
使用示例：
```go
package main

import (
    "gitee.com/johng/gf/g/database/gdb"
    "gitee.com/johng/gf/g/util/gutil"
)

func main() {
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
    db, err := gdb.New()
    if err != nil {
        panic(err)
    }
    // 开启调试模式，以便于记录所有执行的SQL
    db.SetDebug(true)

    // 执行2次查询并将查询结果缓存3秒，并可执行缓存名称(可选)
    for i := 0; i < 2; i++ {
        r, _ := db.Table("user").Cache(3, "vip-user").Where("uid=?", 1).One()
        gutil.Dump(r.ToMap())
    }

    // 执行更新操作，并清理指定名称的查询缓存
    db.Table("user").Cache(-1, "vip-user").Data(gdb.Map{"name" : "smith"}).Where("uid=?", 1).Update()

    // 再次执行查询，启用查询缓存特性
    r, _ := db.Table("user").Cache(3, "vip-user").Where("uid=?", 1).One()
    gutil.Dump(r.ToMap())
}
```
执行后输出结果为（测试表数据结构仅供示例参考）：
```shell
2018-08-31 13:56:58.132 [DEBU] SELECT * FROM user WHERE uid=?, [1], 2018-08-31 13:56:58.131, 2018-08-31 13:56:58.132, 1 ms, DB:Query
{
	"datetime": "",
	"name": "smith",
	"uid": "1"
}

{
	"datetime": "",
	"name": "smith",
	"uid": "1"
}

2018-08-31 13:56:58.144 [DEBU] UPDATE `user` SET `name`=? WHERE uid=?, [smith 1], 2018-08-31 13:56:58.133, 2018-08-31 13:56:58.144, 11 ms, DB:Exec
2018-08-31 13:56:58.144 [DEBU] SELECT * FROM user WHERE uid=?, [1], 2018-08-31 13:56:58.144, 2018-08-31 13:56:58.144, 0 ms, DB:Query
{
	"datetime": "",
	"name": "smith",
	"uid": "1"
}
```


# Record转Struct对象

`gf-orm`为数据表记录操作提供了极高的灵活性和简便性，除了支持以`map`的形式访问/操作数据表记录以外，也支持将数据表记录转换为`struct`进行处理。我们以下使用一个简单的示例来演示该特性。

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

需要注意的是，map中的键名为`uid,name,site`，而struct中的属性为`Uid,Name`，那么他们之间是如何执行映射的呢？主要是以下几点重要的规则：
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

# Result结果集类型转换

`Result/Record`数据类型根据数据结果集操作的需要，往往需要根据记录中**特定的字段**作为键名进行数据检索，因此它包含多个用于转换`Map/List`的方法，同时也包含了常用数据结构`JSON/XML`的转换方法，如下：
```go
// 数据表记录
type Record
    func (r Record) ToMap() Map
    func (r Record) ToJson() string
    func (r Record) ToXml() string
    func (r Record) ToStruct(obj interface{}) error
```

```go
// 数据表记录列表
type Result
    func (r Result) ToList() List
	func (r Result) ToJson() string
    func (r Result) ToXml() string
    func (r Result) ToStringMap(key string) map[string]Map
    func (r Result) ToIntMap(key string) map[int]Map
	func (r Result) ToUintMap(key string) map[uint]Map

    func (r Result) ToStringRecord(key string) map[string]Record
    func (r Result) ToIntRecord(key string) map[int]Record
    func (r Result) ToUintRecord(key string) map[uint]Record
```
由于方法比较简单，这里便不再举例说明。需要注意的是两个方法```Record.ToMap```及```Result.ToList```，这两个方法也是使用比较频繁的方法，用以将ORM查询结果信息转换为可做展示的数据类型。例如，由于结果集字段值底层为```[]byte```类型，虽然使用了新的```Value```类型做了封装，并且也提供了数十种常见的类型转换方法(具体请阅读【[通用动态变量](container/gvar/index.md)】章节)，但是大多数时候需要直接将结果```Result```或者```Record```直接作为```json```或者```xml```数据结构返回，就需要做转换才行。
