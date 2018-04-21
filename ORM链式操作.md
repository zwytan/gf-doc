
[TOC]

>[danger] # 数据库操作

ORM链式操作使用方式简单灵活，是官方推荐的数据库操作方式。

>[success] ## 链式操作

链式操作可以通过数据库对象的```db.Table```/```db.From```方法或者事务对象的```tx.Table```/```tx.From```方法，基于指定的数据表返回一个链式操作对象```*Model```，该对象可以执行以下方法（具体方法说明请参考[API文档](https://godoc.org/github.com/johng-cn/gf/g/database/gdb)）。

```go
LeftJoin(joinTable string, on string) *Model
RightJoin(joinTable string, on string) *Model
InnerJoin(joinTable string, on string) *Model

Fields(fields string) *Model
Limit(start int, limit int) *Model
Data(data...interface{}) *Model
Batch(batch int) *Model

Where(where string, args...interface{}) *Model
And(where interface{}, args ...interface{}) *Model
Or(where interface{}, args ...interface{}) *Model

GroupBy(groupby string) *Model
OrderBy(orderby string) *Model

Insert() (sql.Result, error)
Replace() (sql.Result, error)
Save() (sql.Result, error)
Update() (sql.Result, error)
Delete() (sql.Result, error)

Select() (List, error)
All() (List, error)
One() (Map, error)
Value() (interface{}, error)
```

```Insert/Replace/Save```三个方法的区别：
1. **Insert**
	使用```insert into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，返回失败，否则写入一条新数据；
3. **Replace**
	使用```replace into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，会删除原有的记录，必定会写入一条新记录；
5. **Save**
	使用```insert into```语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，更新原有数据，否则写入一条新数据；

>[success] ## 操作示例

1. **ORM单例对象**
    ```go
    // 获取默认配置的数据库对象(配置名称为"default")
    db, err := gdb.Instance()
    // 获取配置分组名称为"user-center"的数据库对象
    db, err := gdb.Instance("user-center")
    ```
1. **单表/联表查询**
    ```go
    // 查询多条记录并使用Limit分页
    r, err := db.Table("user").Where("u.uid > ?", 1).Limit(0, 10).Select()
    
    // 查询符合条件的单条记录(第一条)
    r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.site").Where("u.uid=?", 1).One()
    
    // 查询指定字段值
    r, err := db.Table("user u").RightJoin("user_detail ud", "u.uid=ud.uid").Fields("ud.site").Where("u.uid=?", 1).Value()
    
    // 分组及排序
    r, err := db.Table("user u").InnerJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.city").GroupBy("city").OrderBy("register_time asc").Select()
    ```
    其中未使用```Fields```方法指定查询字段时，默认查询为```*```。
	支持多种形式的条件参数：
    ```go
    db.Table("user").Where("u.uid=1",).One()
    db.Table("user").Where("u.uid=?", 1).One()
    db.Table("user").Where(g.Map{"uid" : 1}).One()
    db.Table("user").Where("uid=？", 1}).And("name=?", "john").One()
    db.Table("user").Where("uid=？", 1}).Or("name=?", "john").One()
    ```
3. **链式更新/删除**
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
    db.Table("user").Data(`name="john"`).Update()
    db.Table("user").Data("name", "john").Update()
    db.Table("user").Data(g.Map{"name" : "john"}).Update()
    ```
3. **链式写入/保存**
    ```go
    r, err := db.Table("user").Data(gdb.Map{"name": "john"}).Insert()
    r, err := db.Table("user").Data(gdb.Map{"uid": 10000, "name": "john"}).Replace()
    r, err := db.Table("user").Data(gdb.Map{"uid": 10001, "name": "john"}).Save()
    ```

4. **链式批量写入**
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
    r, err := db.Table("user").Data(gdb.List{
        {"name": "john_1"},
        {"name": "john_2"},
        {"name": "john_3"},
        {"name": "john_4"},
    }).Batch(2).Insert()
    ```

5. **链式批量保存**
    ```go
    r, err := db.Table("user").Data(gdb.List{
        {"uid":10000, "name": "john_1"},
        {"uid":10001, "name": "john_2"},
        {"uid":10002, "name": "john_3"},
        {"uid":10003, "name": "john_4"},
    }).Save()
    ```
    

