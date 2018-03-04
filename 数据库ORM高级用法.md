本章节文档正在完善中，文章结构和内容可能会产生比较大的变化，请各位gfer知晓。


[TOC]


>[danger] # 数据库配置文件

如果使用框架封装的单例管理包gins进行管理，那么
数据库配置可以在全局配置文件config.yml中进行配置，例如：
```go
database:
    default:
        - host:     127.0.0.1
          port:     3306
          user:     root
          pass:     123456
          name:     test
          type:     mysql
          role:     master
          charset:  utf-8
          priority: 1
        - host:     127.0.0.2
          port:     3306
          user:     root
          pass:     123456
          name:     test
          type:     mysql
          role:     master
          charset:  utf-8
          priority: 1
```


>[danger] # 数据库方法操作列表

数据库操作接口：
```go
type Link interface {
    Open (c *ConfigNode) (*sql.DB, error)
    Close() error
    Query(q string, args ...interface{}) (*sql.Rows, error)
    Exec(q string, args ...interface{}) (sql.Result, error)
    Prepare(q string) (*sql.Stmt, error)

    GetAll(q string, args ...interface{}) (List, error)
    GetOne(q string, args ...interface{}) (Map, error)
    GetValue(q string, args ...interface{}) (interface{}, error)

    PingMaster() error
    PingSlave() error

    SetMaxIdleConns(n int)
    SetMaxOpenConns(n int)

    setMaster(master *sql.DB)
    setSlave(slave *sql.DB)
    setQuoteChar(left string, right string)
    setLink(link Link)
    getQuoteCharLeft () string
    getQuoteCharRight () string
    handleSqlBeforeExec(q *string) *string

    Begin() (*sql.Tx, error)
    Commit() error
    Rollback() error

    insert(table string, data Map, option uint8) (sql.Result, error)
    Insert(table string, data Map) (sql.Result, error)
    Replace(table string, data Map) (sql.Result, error)
    Save(table string, data Map) (sql.Result, error)

    batchInsert(table string, list List, batch int, option uint8) (sql.Result, error)
    BatchInsert(table string, list List, batch int) (sql.Result, error)
    BatchReplace(table string, list List, batch int) (sql.Result, error)
    BatchSave(table string, list List, batch int) (sql.Result, error)

    Update(table string, data interface{}, condition interface{}, args ...interface{}) (sql.Result, error)
    Delete(table string, condition interface{}, args ...interface{}) (sql.Result, error)

    Table(tables string) (*gLinkOp)
    From(tables string) (*gLinkOp)
}
```

需要说明一下Insert/Replace/Save三者的区别（对于BatchInsert/BatchReplace/BatchSave同理）：
1. **Insert**：使用insert into语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，返回失败，否则写入一条新数据；
2. **Replace**：使用replace into语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，删除原有记录，按照给定数据新写入一条新记录，否则写入一条新数据；
2. **Save**：使用insert into语句进行数据库写入，如果写入的数据中存在Primary Key或者Unique Key的情况，更新原有数据，否则写入一条新数据；

此外，gdb提供简便灵活的链式操作接口，通过gdb.Table/gdb.From方法基于数据表返回一个链式操作对象gLinkOp，该对象定义为非公开对象，可以执行以下方法。



>[danger] # 数据库链接操作列表

链式操作方法列表：
```go
// 链式操作，左联表
func LeftJoin(joinTable string, on string) (*gLinkOp)
// 链式操作，右联表
func RightJoin(joinTable string, on string) (*gLinkOp)
// 链式操作，内联表
func InnerJoin(joinTable string, on string) (*gLinkOp)
// 链式操作，查询字段
func Fields(fields string) (*gLinkOp)
// 链式操作，条件
func Where(where string, args...interface{}) (*gLinkOp)
// 链式操作，group by
func GroupBy(groupby string) (*gLinkOp)
// 链式操作，order by
func OrderBy(orderby string) (*gLinkOp)
// 链式操作，limit
func Limit(start int, limit int) (*gLinkOp)
// 链式操作，操作数据记录项
func Data(data interface{}) (*gLinkOp)
// 链式操作，操作数据记录项列表
func List(list List) (*gLinkOp)
// 链式操作， CURD - Insert/BatchInsert
func Insert() (sql.Result, error)
// 链式操作， CURD - Replace/BatchReplace
func Replace() (sql.Result, error)
// 链式操作， CURD - Save/BatchSave
func Save() (sql.Result, error)
// 链式操作， CURD - Update
func Update() (sql.Result, error)
// 链式操作， CURD - Delete
func Delete() (sql.Result, error)
// 设置批处理的大小
func Batch(batch int) *gLinkOp
// 链式操作，select
func Select() (List, error)
// 链式操作，查询所有记录
func All() (List, error)
// 链式操作，查询单条记录
func One() (Map, error)
// 链式操作，查询字段值
func Value() (interface{}, error)
```















