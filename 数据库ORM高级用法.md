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

