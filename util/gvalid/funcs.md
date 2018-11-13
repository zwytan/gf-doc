# 校验方法

方法列表：
```go
func Check(value interface{}, rules string, msgs interface{}, params ...map[string]interface{}) *Error
func CheckMap(params map[string]interface{}, rules interface{}, msgs ...CustomMsg) *Error
func CheckStruct(object interface{}, rules interface{}, msgs ...CustomMsg) *Error

type Error
    func (e *Error) FirstItem() (key string, msgs map[string]string)
    func (e *Error) FirstRule() (rule string, err string)
    func (e *Error) FirstString() (err string)
    func (e *Error) Map() map[string]string
    func (e *Error) Maps() ErrorMap
    func (e *Error) String() string
    func (e *Error) Strings() (errs []string)

```
简要说明：
1. `Check`方法用于单条数据校验，比较简单，方法详细介绍请看后续章节；
1. `CheckMap`方法用于多条数据校验，校验的主体变量为`map`类型，方法详细介绍请看后续章节；
1. `CheckStruct`方法用于多条数据校验，校验的主体变量为结构体对象类型，方法详细介绍请看后续章节；
1. `Check*`方法只有在返回`nil`的情况下，表示数据校验成功，否则返回校验出错的错误信息对象指针`*Error`；


# Error校验结果

校验结果对象`Error`包含多个字段及其规则及对应错误信息的层级map，底层的错误信息存放数据结构如下：
```go
// 校验错误信息: map[键名]map[规则名]错误信息
type ErrorMap map[string]map[string]string
```
可以结合后续的示例理解这个数据结构。我们可以通过`Maps()`方法获得该原始错误信息数据结构map。但在大多数时候我们可以通过`Error`对象的其他方法来方便地获取特定的错误信息。

