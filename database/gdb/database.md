
# 数据库类型

由于go标准库的数据库操作对象采用接口实现，因此提供了非常好的可扩展性和兼容性。

## MySQL

内置支持，无需额外扩展或第三方包接入，直接可用。
依赖的第三方包：https://github.com/go-sql-driver/mysql

## SQLite

在使用时需要引入第三方包 ([ go-sqlite3](https://github.com/mattn/go-sqlite3) )：
```go
_ "github.com/mattn/go-sqlite3"
```
### 限制
1. 不支持`Save/Replace`方法

## PostgreSQL

在使用时需要引入第三方包 ([pq](https://github.com/lib/pq) )：
```go
_ "github.com/lib/pq"
```
### 限制
1. 不支持`Save/Replace`方法

## Oracle

使用时需导入第三方包 ([go-oci8](https://github.com/mattn/go-oci8) )：
```go
_ "github.com/mattn/go-oci8"
```
### 限制
1. 不支持`LastInsertId`方法
2. 不支持`Save/Replace`方法

## SQL Server

使用时需导入第三方包 ([go-mssqldb](https://github.com/denisenkom/go-mssqldb) )：
```go
_ "github.com/denisenkom/go-mssqldb"
```

### 限制
1. 不支持`LastInsertId`方法
2. 不支持`Save/Replace`方法
3. 仅支持`SQL Server 2005`及其后的版本



## 其他数据库的支持

额外接入新的数据库相当方便，可参考源码中关于`PostgreSQL`、`SQLite`、`Oracle`、`SQL Server`的接入方式。
