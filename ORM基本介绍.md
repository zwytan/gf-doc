>[danger] # 基本介绍

gf-ORM的最大特色是底层数据结构采用了map而不是struct来存储数据表记录（当然也支持数据表记录与struct的映射转换，具体请查看后续【[ORM高级特性](ORM高级用法.md)】章节）。为便于数据表记录的操作，ORM定义了5种基本的数据类型：

```go
type Map         map[string]interface{} // 数据记录
type List        []Map                  // 数据记录列表 

type Value       string                 // 返回数据表记录值
type Record      map[string]Value       // 返回数据表记录键值对
type Result      []Record               // 返回数据表记录列表
```

1. ```Map```与```List```用于ORM操作过程中的输入参数类型（与全局类型```g.Map```和```g.List```一致，在项目开发中常用g.Map和g.List替换）；
2. Value/Record/Result用于ORM操作的结果集数据类型，其中```Result```表示数据表记录列表，```Record```表示一条数据表记录，```Value```表示记录中的一条键值数据；