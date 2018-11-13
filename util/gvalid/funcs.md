# 校验方法

相关数据结构：
```go
// 校验错误对象
type Error map[string]map[string]string
```

校验方法列表：
```go
func Check(val interface{}, rules string, msgs interface{}, params ...map[string]interface{}) Error
func CheckMap(params map[string]interface{}, rules map[string]string, msgs ...map[string]interface{}) Error
func CheckStruct(st interface{}, rules map[string]string, msgs ...map[string]interface{}) Error
func SetDefaultErrorMsgs(msgs map[string]string)
```
`Check*`方法只有在返回nil的情况下，表示数据校验成功，否则返回校验出错的数据项(```CheckMap```)以及对应的规则和错误信息的map，具体请查看后续示例代码更容易理解。