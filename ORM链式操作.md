
[TOC]

>[danger] # 数据库操作

gdb数据库链式操作的方法比较多，具体详见[API文档](https://godoc.org/github.com/johng-cn/gf/g/database/gdb)，以下仅对一些常用的方法进行介绍。

>[success] ## 链式操作

数据表操作推荐尽量使用链式操作实现。gdb提供简便灵活的链式操作接口，通过数据库对象的db.Table/db.From方法或者事务对象的tx.Table/tx.From方法基于指定的数据表返回一个链式操作对象DbOp，该对象可以执行以下方法（具体方法说明请参考[API文档](https://godoc.org/github.com/johng-cn/gf/g/database/gdb)）。

```go
func LeftJoin(joinTable string, on string) (*DbOp)
func RightJoin(joinTable string, on string) (*DbOp)
func InnerJoin(joinTable string, on string) (*DbOp)

func Fields(fields string) (*DbOp)
func Limit(start int, limit int) (*DbOp)
func Data(data interface{}) (*DbOp)
func Batch(batch int) *DbOp

func Where(where string, args...interface{}) (*DbOp)
func GroupBy(groupby string) (*DbOp)
func OrderBy(orderby string) (*DbOp)

func Insert() (sql.Result, error)
func Replace() (sql.Result, error)
func Save() (sql.Result, error)
func Update() (sql.Result, error)
func Delete() (sql.Result, error)


func Select() (List, error)
func All() (List, error)
func One() (Map, error)
func Value() (interface{}, error)
```


>[success] ## 链式操作示例

1. **链式查询**
    ```go
    // 查询多条记录并使用Limit分页
    r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*, ud.site").Where("u.uid > ?", 1).Limit(0, 10).Select()
    // 查询符合条件的单条记录(第一条)
    r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.site").Where("u.uid=?", 1).One()
    // 查询字段值
    r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("ud.site").Where("u.uid=?", 1).Value()
    // 分组及排序
    r, err := db.Table("user u").LeftJoin("user_detail ud", "u.uid=ud.uid").Fields("u.*,ud.city").GroupBy("city").OrderBy("register_time asc").Select()
    ```

2. **链式更新/删除**
    ```go
    // 更新
    r, err := db.Table("user").Data(gdb.Map{"name" : "john2"}).Where("name=?", "john").Update()
    r, err := db.Table("user").Data("name='john3'").Where("name=?", "john2").Update()
    // 删除
    r, err := db.Table("user").Where("uid=?", 10).Delete()
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
	可以指定批量操作中分批写入数据库的每批次写入条数数量：
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
    

