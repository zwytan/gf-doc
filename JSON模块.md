>[danger] # gjson

常用方法列表如下：
```go
func Decode(b []byte) (interface{}, error)
func DecodeTo(b []byte, v interface{}) error
func Encode(v interface{}) ([]byte, error)
type Json
    func DecodeToJson(b []byte) (*Json, error)
    func Load(path string) (*Json, error)
    func NewJson(v *interface{}) *Json
    func (p *Json) Get(pattern string) interface{}
    func (p *Json) GetArray(pattern string) []interface{}
    func (p *Json) GetBool(pattern string) bool
    func (p *Json) GetFloat32(pattern string) float32
    func (p *Json) GetFloat64(pattern string) float64
    func (p *Json) GetInt(pattern string) int
    func (p *Json) GetJson(pattern string) *Json
    func (p *Json) GetMap(pattern string) map[string]interface{}
    func (p *Json) GetString(pattern string) string
    func (p *Json) GetToVar(pattern string, v interface{}) error
    func (p *Json) GetUint(pattern string) uint
    func (p *Json) ToArray() []interface{}
    func (p *Json) ToMap() map[string]interface{}
```

gjson提供了非常灵活的json解析及数据层级读取特性。我们来看一个例子：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/encoding/gjson"
)

func main() {
    data := `{
        "users" : {
                "count" : 100,
                "list"  : [
                    {"name" : "小明",  "score" : 60},
                    {"name" : "John", "score" : 99.5}
                ]
            }
    }`
    j, err := gjson.DecodeToJson([]byte(data))
    if err != nil {
        glog.Error(err)
    } else {
        fmt.Println("John Score:", j.GetFloat32("users.list.1.score"))
    }
}
```

可以看到，gjson.Json对象可以通过非常灵活的层级筛选功能(```j.GetFloat32("users.list.1.score")```)检索到对应的变量信息。

这也是gjson包的一大特色。