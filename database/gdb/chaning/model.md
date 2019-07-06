
[TOC]


# 1. 获取ORM对象
```go
// 获取默认配置的数据库对象(配置名称为"default")
db := g.Database()
// 或者(别名方式, 推荐)
db := g.DB()

// 获取配置分组名称为"user-center"的数据库对象
db := g.Database("user-center")
// 或者 (别名方式, 推荐)
db := g.DB("user-center")

// 使用原生New方法创建数据库对象
db, err := gdb.New()
db, err := gdb.New("user-center")

// 使用原生单例管理方法获取数据库对象单例
db, err := gdb.Instance()
db, err := gdb.Instance("user-center")

// 注意不用的时候不需要使用Close方法关闭数据库连接(并且gdb也没有提供Close方法)，
// 数据库引擎底层采用了链接池设计，当链接不再使用时会自动关闭
```
# 2. 单表/联表查询

> TIPS: `Where`条件参数推荐使用字符串的传递方式（使用`?`占位符预处理），因为`map`/`struct`类型作为查询参数无法保证顺序性，且在部分情况下（数据库有时会帮助你自动进行查询索引优化），数据库的索引和你传递的查询条件顺序有一定关系。

条件连接符号可以使用`Where`/`And`/`Or`，其中当使用多个`Where`方法连接查询条件时，作用同`And`。
此外，当存在多个查询条件时，`gdb`会默认将多个条件分别使用`()`符号进行包含，这种设计可以非常友好地支持查询条件分组。

## 1). 基础查询
`Where + string`，条件参数使用字符串和预处理。
```go
// 查询多条记录并使用Limit分页
// SELECT * FROM user WHERE uid>1 LIMIT 0,10
r, err := db.Table("user").Where("uid > ?", 1).Limit(0, 10).Select()

// 使用Fields方法查询指定字段
// 未使用Fields方法指定查询字段时，默认查询为*
// SELECT uid,name FROM user WHERE uid>1 LIMIT 0,10
r, err := db.Table("user").Fileds("uid,name").Where("uid > ?", 1).Limit(0, 10).Select()

// 支持多种Where条件参数类型
// SELECT * FROM user WHERE uid=1
r, err := db.Table("user").Where("u.uid=1",).One()
r, err := db.Table("user").Where("u.uid", 1).One()
r, err := db.Table("user").Where("u.uid=?", 1).One()
// SELECT * FROM user WHERE (uid=1) AND (name='john')
r, err := db.Table("user").Where("uid", 1).Where("name", "john").One()
r, err := db.Table("user").Where("uid=?", 1).And("name=?", "john").One()
// SELECT * FROM user WHERE (uid=1) OR (name='john')
r, err := db.Table("user").Where("uid=?", 1).Or("name=?", "john").One()
```
`Where + map`，条件参数使用任意`map`类型传递。
```go
// SELECT * FROM user WHERE uid=1 AND name='john'
r, err := db.Table("user").Where(g.Map{"uid" : 1, "name" : "john"}).One()
// SELECT * FROM user WHERE uid=1 AND age>18
r, err := db.Table("user").Where(g.Map{"uid" : 1, "age>" : 18}).One()
```
`Where + struct/*struct`，`struct`标签支持 `gconv/json`，映射属性到字段名称关系。
```go
type User struct {
    Id       int    `json:"uid"`
    UserName string `gconv:"name"`
}
// SELECT * FROM user WHERE uid =1 AND name='john'
r, err := db.Table("user").Where(User{ Id : 1, UserName : "john"}).One()
// SELECT * FROM user WHERE uid =1
r, err := db.Table("user").Where(&User{ Id : 1}).One()
```

以上的查询条件相对比较简单，我们来看一个比较复杂的查询示例。
```go
conditions := g.Map{
    "title like ?"         : "%九寨%",
    "online"               : 1,
    "hits between ? and ?" : g.Slice{1, 10},
    "exp > 0"              : nil,
    "category"             : g.Slice{100, 200},
}
result, err := db.Table("article").Where(conditions).All()
// SELECT * FROM article WHERE title like '%九寨%' AND online=1 AND hits between 1 and 10 AND exp > 0 AND category IN(100,200)
```

## 2). `join`查询
```go
// 查询符合条件的单条记录(第一条)
// SELECT u.*,ud.site FROM user u LEFT JOIN user_detail ud ON u.uid=ud.uid WHERE u.uid=1 LIMIT 1
r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.site").Where("u.uid=?", 1).One()

// 查询指定字段值
// SELECT ud.site FROM user u RIGHT JOIN user_detail ud ON u.uid=ud.uid WHERE u.uid=1 LIMIT 1
r, err := db.Table("user u").RightJoin("user_detail ud", "u.uid=ud.uid").Fields("ud.site").Where("u.uid=?", 1).Value()

// 分组及排序
// SELECT u.*,ud.site FROM user u INNER JOIN user_detail ud ON u.uid=ud.uid GROUP BY city ORDER BY register_time asc
r, err := db.Table("user u").InnerJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.city").GroupBy("city").OrderBy("register_time asc").Select()

// 不使用join的联表查询
// SELECT u.*,ud.city FROM user u,user_detail ud WHERE u.uid=ud.uid
r, err := db.Table("user u,user_detail ud").Where("u.uid=ud.uid").Fields("u.*,ud.city").All()
```

## 3). `select in`查询
使用字符串、`slice`参数类型。当使用`slice`参数类型时，预处理占位符只需要一个`?`即可。
```go
// SELECT * FROM user WHERE uid IN(100,10000,90000)
r, err := db.Table("user").Where("uid IN(?,?,?)", 100, 10000, 90000).All()
// SELECT * FROM user WHERE gender=1 AND uid IN(100,10000,90000)
r, err := db.Table("user").Where("gender=? AND uid IN(?)", 1, g.Slice{100, 10000, 90000}).All()
// SELECT COUNT(*) FROM user WHERE age in(18,50)
r, err := db.Table("user").Where("age IN(?,?)", 18, 50).Count()
```
使用任意`map`参数类型。
```go
// SELECT * FROM user WHERE gender=1 AND uid IN(100,10000,90000)
r, err := db.Table("user").Where(g.Map{
    "gender" : 1,
    "uid"    : g.Slice{100,10000,90000},
}).All()
```
使用`struct`参数类型，注意查询条件的顺序和`struct`的属性定义顺序有关。
```go
type User struct {
    Id     []int  `gconv:"uid"`
    Gender int    `gconv:"gender"`
}
// SELECT * FROM user WHERE uid IN(100,10000,90000) AND gender=1
r, err := db.Table("user").Where(User{
    "gender" : 1,
    "uid"    : []int{100, 10000, 90000},
}).All()
```

## 4). `like`查询
```go
// SELECT * FROM user WHERE name like '%john%'
r, err := db.Table("user").Where("name like ?", "%john%").Select()
// SELECT * FROM user WHERE birthday like '1990-%'
r, err := db.Table("user").Where("birthday like ?", "1990-%").Select()
```

## 5). `sum`查询
```go
// SELECT SUM(score) FROM user WHERE uid=1
r, err := db.Table("user").Fields("SUM(score)").Where("uid=?", 1).Value()
```

## 6). `count`查询
```go
// SELECT COUNT(1) FROM user WHERE `birthday`='1990-10-01'
r, err := db.Table("user").Where("birthday=?", "1990-10-01").Count()
// SELECT COUNT(uid) FROM user WHERE `birthday`='1990-10-01'
r, err := db.Table("user").Fields("uid").Where("birthday=?", "1990-10-01").Count()
```

## 7). `distinct`查询
```go
// SELECT DISTINCT uid,name FROM user 
r, err := db.Table("user").Fields("DISTINCT uid,name").Select()
```

# 3. 链式更新/删除
```go
// UPDATE user SET name='john guo' WHERE name='john'
r, err := db.Table("user").Data(gdb.Map{"name" : "john guo"}).Where("name=?", "john").Update()
r, err := db.Table("user").Data("name='john guo'").Where("name=?", "john").Update()
// UPDATE user SET status=1 ORDER BY login_time asc LIMIT 10
r, err := db.Table("user").Data("status", 1).OrderBy("login_time asc").Limit(10).Update

// DELETE FROM user WHERE uid=10
r, err := db.Table("user").Where("uid=?", 10).Delete()
// DELETE FROM user ORDER BY login_time asc LIMIT 10
r, err := db.Table("user").OrderBy("login_time asc").Limit(10).Delete()
```
其中```Data```是数值方法，用于指定写入/更新/批量写入/批量更新的数值。
支持多种形式的数值参数：
```go
r, err := db.Table("user").Data("status=1").Update()
r, err := db.Table("user").Data("status", 1).Update()
r, err := db.Table("user").Data(g.Map{"status" : 1}).Update()
```
# 4. 链式写入/保存
```go
r, err := db.Table("user").Data(gdb.Map{"name": "john"}).Insert()
r, err := db.Table("user").Data(g.Map{"uid": 10000, "name": "john"}).Replace()
r, err := db.Table("user").Data(g.Map{"uid": 10001, "name": "john"}).Save()
```
其中，数值方法参数既可以使用`gdb.Map`，也可以使用`g.Map`。

# 5. 链式批量写入
```go
r, err := db.Table("user").Data(gdb.List{
    {"name": "john_1"},
    {"name": "john_2"},
    {"name": "john_3"},
    {"name": "john_4"},
}).Insert()
```
可以通过```Batch```方法指定批量操作中分批写入条数数量：
```go
r, err := db.Table("user").Data(g.List{
    {"name": "john_1"},
    {"name": "john_2"},
    {"name": "john_3"},
    {"name": "john_4"},
}).Batch(2).Insert()
```
当然，`gdb.List`类型也可以使用`g.List`类型。

# 6. 链式批量保存
```go
r, err := db.Table("user").Data(gdb.List{
    {"uid":10000, "name": "john_1"},
    {"uid":10001, "name": "john_2"},
    {"uid":10002, "name": "john_3"},
    {"uid":10003, "name": "john_4"},
}).Save()
```

# 7. 参数自动过滤
`gdb`可以自动同步**数据表结构**到程序缓存中(缓存不过期，直至程序重启/重新部署)，并且可以过滤提交参数中不符合表结构的数据项，该特性可以使用`Filter`方法实现，例如:
```go
r, err := db.Table("user").Filter().Data(g.Map{
    "id"          : 1,
    "uid"         : 1,
    "passport"    : "john",
    "password"    : "123456",
}).Insert()
// INSERT INTO user(uid,passport,password) VALUES(1, "john", "123456")
```
其中`id`为不存在的字段，在写入数据时将会被过滤掉，不至于被构造成写入SQL中产生执行错误。

# 8. 查询结果处理

请参考【[ORM结果处理](database/gdb/result.md)】章节。
