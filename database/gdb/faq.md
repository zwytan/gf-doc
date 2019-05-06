[TOC]

# 1. 数据库查询结果转`json`没有了数值

将数据库数据集进行`json`编码但是返回结果中没有值，如：
```go
r, _ := db.Table("user").Where("uid=?", 1).One()
if r != nil {
    b, _ := gjson.Encode(r)
    fmt.Println(string(b))
}
```
结果为：
```json
{"email":{},"name":{},"type":{},"uid":{}}
```

**回答：**

1. 标准库原生的结果集数值为`[]byte`类型，`gdb`为了方便开发者转换数据格式，将结果集数值改进为了`gvar.VarRead`只读对象，因此在以上结果中看到的数值为一个对象的`{}`；
1. 以上代码中我们可以方便地通过`r.ToJson()`来进行数据格式转换，当然`gdb`的结果集支持多种数据格式转换，详细请参考【[ORM高级特性](database/gdb/senior)】章节；
1. `gvar.VarRead`对象的详细介绍请参考【[gvar通用动态变量](container/gvar/index)】章节；

# 2. 表字段类型为`datetime`，参数为`time.Time`类型，写入后时区不对

以下程序：
```go
r, err := db.Table("user").Data(g.Map{
    "name"        : "john",
    "create_time" : time.Now(),
}).Insert()
if err == nil {
    fmt.Println(r.LastInsertId())
} else {
    panic(err)
}
```
写入成功后，查询写入的数据`create_time`比系统时间少了8小时，系统为+8时区。


**回答：**

这是因为当传递的参数为`time.Time`类型时，`go-mysql`引擎底层会默认使用`UTC`时间进行保存，你可以将参数给定为字符串来解决这个问题，例如：
```go
r, err := db.Table("user").Data(g.Map{
    "name"        : "john",
    "create_time" : gtime.Now().String(),
}).Insert()
if err == nil {
    fmt.Println(r.LastInsertId())
} else {
    panic(err)
}
```
其中`gtime.Now().String()`生成的是以当前程序设置时区(没有设置时默认为系统时区)生成的时间字符串，例如：`2018-11-11 12:00:00`。