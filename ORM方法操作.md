
[TOC]

>[danger] # 数据库操作

ORM方法操作相对链式操作更偏底层操作一些，在项目开发中常用链式操作，因为链式操作更简单灵活，但链式操作执行不了太过于复杂的SQL操作，可以交给方法操作来处理。

>[success] ## 方法操作

```go
// SQL操作方法，返回原生的标准库sql对象
Query(query string, args ...interface{}) (*sql.Rows, error)
Exec(query string, args ...interface{}) (sql.Result, error)
Prepare(query string) (*sql.Stmt, error)

// 数据表记录查询：
// 查询单条记录、查询多条记录、获取记录对象、查询单个字段值(链式操作同理)
GetAll(query string, args ...interface{}) (Result, error)
GetOne(query string, args ...interface{}) (Record, error)
GetStruct(obj interface{}, query string, args ...interface{}) error
GetValue(query string, args ...interface{}) (Value, error)
Select(tables, fields string, condition interface{}, groupBy, orderBy string, first, limit int, args ...interface{}) (Result, error)

// 开启事务操作
Begin() (*Tx, error)

// 数据单条操作
Insert(table string, data Map) (sql.Result, error)
Replace(table string, data Map) (sql.Result, error)
Save(table string, data Map) (sql.Result, error)

// 数据批量操作
BatchInsert(table string, list List, batch int) (sql.Result, error)
BatchReplace(table string, list List, batch int) (sql.Result, error)
BatchSave(table string, list List, batch int) (sql.Result, error)

// 数据修改/删除
Update(table string, data interface{}, condition interface{}, args ...interface{}) (sql.Result, error)
Delete(table string, condition interface{}, args ...interface{}) (sql.Result, error)

// 创建链式操作对象(Table为From的别名)
Table(tables string) (*Model)
From(tables string) (*Model)
    
// 关闭数据库
Close() error
```

>[success] ## 操作示例


1. **ORM对象**
    ```go
    // 获取默认配置的数据库对象(配置名称为"default")
    db, err := gdb.New()
    // 获取配置分组名称为"user-center"的数据库对象
    db, err := gdb.New("user-center")
    ```

2. **数据写入**
    ```go
    r, err := db.Insert("user", gdb.Map {
        "name": "john",
    })
    ```

3. **数据查询(列表)**
    ```go
    list, err := db.GetAll("select * from user limit 2")
    list, err := db.Select("user", "*", nil, "", "", 0, 2)
    ```

4. **数据查询(单条)**
    ```go
    one, err := db.GetOne("select * from user limit 2")
    // 或者
    one, err := db.GetOne("select * from user where uid=1000")
    ```

5. **数据保存**
    ```go
    r, err := db.Save("user", gdb.Map {
        "uid"  :  1,
        "name" : "john",
    })
    ```

6. **批量操作**
    ```go
    // BatchInsert/BatchReplace/BatchSave 同理
    _, err := db.BatchInsert("user", gdb.List {
        {"name": "john_1"},
        {"name": "john_2"},
        {"name": "john_3"},
        {"name": "john_4"},
    }, 10)
    ```

7. **数据更新/删除**
    ```go
    // db.Update/db.Delete 同理
    r, err := db.Update("user", gdb.Map {"name": "john"}, "uid=?", 10000)
    r, err := db.Update("user", "name='john'", "uid=10000")
    r, err := db.Update("user", "name=?", "uid=?", "john", 10000)
    ```
	注意，参数域支持并建议使用预处理模式进行输入，避免SQL注入风险。
