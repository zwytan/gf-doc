
# 数据库类型

由于go标准库的数据库操作对象采用接口实现，因此提供了非常好的可扩展性和兼容性。

## MySQL

内置支持，无需额外扩展或第三方包接入，直接可用。

## SQLite

在使用时需要引入第三方包：
```go
_ "github.com/mattn/go-sqlite3"
```
### 限制
1. 不支持`Save/Replace`方法

## PostgreSQL

在使用时需要引入第三方包：
```go
_ "github.com/lib/pq"
```
### 限制
1. 不支持`Save/Replace`方法

## Oracle

使用时需导入第三方包：
```go
_ "github.com/mattn/go-oci8"
```
### 限制
1. 不支持`LastInsertId`方法
2. 不支持`Save/Replace`方法

## Mssql

使用时需导入第三方包：
```go
_ "github.com/denisenkom/go-mssqldb"
```

### 限制
1. 不支持`LastInsertId`方法
2. 不支持`Save/Replace`方法



## 其他数据库的支持

额外接入新的数据库相当方便，可参考源码中关于`PostgreSQL`、`SQLite`、`Oracle`、`Mssql`的接入方式。
