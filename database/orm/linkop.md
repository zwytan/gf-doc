
[TOC]

# 数据库操作

ORM链式操作使用方式简单灵活，是官方推荐的数据库操作方式。

## 链式操作

链式操作可以通过数据库对象的```db.Table```/```db.From```方法或者事务对象的```tx.Table```/```tx.From```方法，基于指定的数据表返回一个链式操作对象```*Model```，该对象可以执行以下方法（具体方法说明请参考[API文档](https://godoc.org/github.com/johng-cn/gf/g/database/gdb)）。

```go
func (md *Model) LeftJoin(joinTable string, on string) *Model
func (md *Model) RightJoin(joinTable string, on string) *Model
func (md *Model) InnerJoin(joinTable string, on string) *Model

func (md *Model) Fields(fields string) *Model
func (md *Model) Limit(start int, limit int) *Model
func (md *Model) Data(data...interface{}) *Model
func (md *Model) Batch(batch int) *Model

func (md *Model) Where(where string, args...interface{}) *Model
func (md *Model) And(where interface{}, args ...interface{}) *Model
func (md *Model) Or(where interface{}, args ...interface{}) *Model

func (md *Model) GroupBy(groupby string) *Model
func (md *Model) OrderBy(orderby string) *Model

func (md *Model) Insert() (sql.Result, error)
func (md *Model) Replace() (sql.Result, error)
func (md *Model) Save() (sql.Result, error)
func (md *Model) Update() (sql.Result, error)
func (md *Model) Delete() (sql.Result, error)

func (md *Model) Select() (Result, error)
func (md *Model) All() (Result, error)
func (md *Model) One() (Record, error)
func (md *Model) Value() (Value, error)
func (md *Model) Count() (int, error)
func (md *Model) Struct(obj interface{}) error

func (md *Model) Chunk(limit int, callback func(result Result, err error) bool)
func (md *Model) ForPage(page, limit int) (*Model)
```

`Insert/Replace/Save`三个方法的区别：
1. **Insert**
	使用```insert into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，返回失败，否则写入一条新数据；
3. **Replace**
	使用```replace into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，会删除原有的记录，必定会写入一条新记录；
5. **Save**
	使用```insert into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，更新原有数据，否则写入一条新数据；

## 操作示例

### 1. 获取ORM对象
```go
// 获取默认配置的数据库对象(配置名称为"default")
db, err := gdb.New()
// 或者
db := g.Database()
// 或者(别名方式, 推荐)
db := g.DB()

// 获取配置分组名称为"user-center"的数据库对象
db, err := gdb.New("user-center")
// 或者 
db := g.Database("user-center")
// 或者 (别名方式)
db := g.DB("user-center")

// 注意不用的时候不需要使用Close方法关闭数据库连接(并且gdb也没有提供Close方法)，
// 数据库引擎底层采用了链接池设计，当链接不再使用时会自动关闭
```
### 2. 单表/联表查询
```go
// 查询多条记录并使用Limit分页
r, err := db.Table("user").Where("u.uid > ?", 1).Limit(0, 10).Select()

// 查询符合条件的单条记录(第一条)
r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.site").Where("u.uid=?", 1).One()

// 查询指定字段值
r, err := db.Table("user u").RightJoin("user_detail ud", "u.uid=ud.uid").Fields("ud.site").Where("u.uid=?", 1).Value()

// 分组及排序
r, err := db.Table("user u").InnerJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.city").GroupBy("city").OrderBy("register_time asc").Select()

// 不使用john的联表查询
r, err := db.Table("user u,user_detail ud").Where("u.uid=ud.uid").Fields("u.*,ud.city").All()
```
其中未使用```Fields```方法指定查询字段时，默认查询为```*```。
支持多种形式的条件参数：
```go
r, err := db.Table("user").Where("u.uid=1",).One()
r, err := db.Table("user").Where("u.uid=?", 1).One()
r, err := db.Table("user").Where(g.Map{"uid" : 1}).One()
r, err := db.Table("user").Where("uid=？", 1).And("name=?", "john").One()
r, err := db.Table("user").Where("uid=？", 1).Or("name=?", "john").One()
```

### 3. `in`查询
```go
// SELECT * FROM user WHERE uid IN(100,10000,90000)
r, err := db.Table("user").Where("uid IN(?,?,?)", 100, 10000, 90000).All()
// SELECT * FROM user WHERE gender=1 AND uid IN(100,10000,90000)
r, err := db.Table("user").Where("gender=? AND uid IN(?)", 1, g.Slice{100, 10000, 90000}).All()
// SELECT COUNT(*) FROM user WHERE age in(18,50)
r, err := db.Table("user").Where("age IN(?,?)", 18, 50).Count()
```

### 4. `like`查询
```go
// SELECT * FROM user WHERE name like '%john%'
r, err := db.Table("user").Where("name like ?", "%john%").Select()
// SELECT * FROM user WHERE birthday like '1990-%'
r, err := db.Table("user").Where("birthday like ?", "1990-%").Select()
```

### 5. `sum`查询
```go
// SELECT SUM(score) FROM user WHERE uid=1
r, err := db.Table("user").Fields("SUM(score)").Where("uid=?", 1).Value()
```

### 6. `count`查询
```go
// SELECT COUNT(1) FROM user WHERE `birthday`='1990-10-01'
r, err := db.Table("user").Where("birthday=?", "1990-10-01").Count()
// SELECT COUNT(uid) FROM user WHERE `birthday`='1990-10-01'
r, err := db.Table("user").Fields("uid").Where("birthday=?", "1990-10-01").Count()
```

### 7. `distinct`查询
```go
// SELECT DISTINCT uid,name FROM user 
r, err := db.Table("user").Fields("DISTINCT uid,name").Select()
```

### 8. 链式更新/删除
```go
// 更新
r, err := db.Table("user").Data(gdb.Map{"name" : "john2"}).Where("name=?", "john").Update()
r, err := db.Table("user").Data("name='john3'").Where("name=?", "john2").Update()
// 删除
r, err := db.Table("user").Where("uid=?", 10).Delete()
```
其中```Data```是数值方法，用于指定写入/更新/批量写入/批量更新的数值。
支持多种形式的数值参数：
```go
r, err := db.Table("user").Data(`name="john"`).Update()
r, err := db.Table("user").Data("name", "john").Update()
r, err := db.Table("user").Data(g.Map{"name" : "john"}).Update()
```
### 9. 链式写入/保存
```go
r, err := db.Table("user").Data(gdb.Map{"name": "john"}).Insert()
r, err := db.Table("user").Data(g.Map{"uid": 10000, "name": "john"}).Replace()
r, err := db.Table("user").Data(g.Map{"uid": 10001, "name": "john"}).Save()
```
其中，数值方法参数既可以使用`gdb.Map`，也可以使用`g.Map`。

### 10. 链式批量写入
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

### 11. 链式批量保存
```go
r, err := db.Table("user").Data(gdb.List{
    {"uid":10000, "name": "john_1"},
    {"uid":10001, "name": "john_2"},
    {"uid":10002, "name": "john_3"},
    {"uid":10003, "name": "john_4"},
}).Save()
```

### 12. 参数过滤功能
`gform`可以自动同步**数据表结构**到程序缓存中(缓存不过期，直至程序重启/重新部署)，并且可以过滤提交参数中不符合表结构的数据项，该特性可以使用`Filter`方法实现，例如:
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

### 13. 查询结果转换为`Json/Xml`
```go
one, err := db.Table("user").Where("uid=?", 1).One()
if err != nil {
    panic(err)
}

// 使用内置方法转换为json/xml
fmt.Println(one.ToJson())
fmt.Println(one.ToXml())

// 自定义方法方法转换为json/xml
jsonContent, _ := gparser.VarToJson(one.ToMap())
fmt.Println(jsonContent)
xmlContent, _  := gparser.VarToXml(one.ToMap())
fmt.Println(xmlContent)
```
