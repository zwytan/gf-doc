>[danger] # gjson

强大的JSON编码/解析模块。

使用方式：
```go
import "gitee.com/johng/gf/g/encoding/gjson"
```

方法列表：
```go
func Decode(b []byte) (interface{}, error)
func DecodeTo(b []byte, v interface{}) error
func Encode(v interface{}) ([]byte, error)
type Json
    func DecodeToJson(b []byte) (*Json, error)
    func Load(path string) (*Json, error)
    func LoadContent(data []byte, t string) (*Json, error)
    func New(v *interface{}) *Json
    func (j *Json) Get(pattern string) interface{}
    func (j *Json) GetArray(pattern string) []interface{}
    func (j *Json) GetBool(pattern string) bool
    func (j *Json) GetFloat32(pattern string) float32
    func (j *Json) GetFloat64(pattern string) float64
    func (j *Json) GetInt(pattern string) int
    func (j *Json) GetJson(pattern string) *Json
    func (j *Json) GetMap(pattern string) map[string]interface{}
    func (j *Json) GetString(pattern string) string
    func (j *Json) GetToVar(pattern string, v interface{}) error
    func (j *Json) GetUint(pattern string) uint
    func (j *Json) Set(pattern string, value interface{}) error
    func (j *Json) ToArray() []interface{}
    func (j *Json) ToJson() ([]byte, error)
    func (j *Json) ToJsonIndent() ([]byte, error)
    func (j *Json) ToMap() map[string]interface{}
    func (j *Json) ToToml() ([]byte, error)
    func (j *Json) ToXml(rootTag ...string) ([]byte, error)
    func (j *Json) ToXmlIndent(rootTag ...string) ([]byte, error)
    func (j *Json) ToYaml() ([]byte, error)
    func (j *Json) ToStruct(o interface{}) error
```

gjson的特点：
1、支持数据层级检索；
2、支持运行时数据修改；
3、支持JSON、XML、YAML/YML、TOML数据格式相互转换；

使用方式：

```go
import "gitee.com/johng/gf/g/encoding/gjson"
```


1. **数据层级检索**

    ```go
    data :=
        `{
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
    ```

    可以看到，gjson.Json对象可以通过非常灵活的层级筛选功能(```j.GetFloat32("users.list.1.score")```)检索到对应的变量信息。这是gjson包的一大特色。
    
2. **处理键名本身带有层级符号"."的情况**
    ```go
    data :=
        `{
            "users" : {
                "count" : 100
            },
            "users.count" : 101
        }`
    j, err := gjson.DecodeToJson([]byte(data))
    if err != nil {
        glog.Error(err)
    } else {
        fmt.Println("Users Count:", j.GetInt("users.count"))
    }
    ```
    运行之后打印出的结果为```101```。当键名存在"."号时，检索优先级：键名->层级，因此并不会引起歧义。
    
1. **支持运行时的数据修改**
    ```go
    data :=
        `{
            "users" : {
                "count" : 100
            }
        }`
    j, err := gjson.DecodeToJson([]byte(data))
    if err != nil {
        glog.Error(err)
    } else {
        j.Set("users.count",  2)
        j.Set("users.list",  []string{"John", "小明"})
        c, _ := j.ToJson()
        fmt.Println(string(c))
    }
    ```
    执行后输出结果为：
    ```{"users":{"count":1,"list":["John","小明"]}}```
	当JSON数据通过gjson包读取后，可以通过Set方法改变内部变量的内容，当然也可以新增/删除内容，当需要删除内容时，设定的值为nil即可。gjson包的数据运行时修改特性非常强大，在该特性的支持下，各种数据结构的编码/解析显得异常的灵活方便。

    
3. **JSON转换为其他数据格式**
    ```go
    data :=
        `{
            "users" : {
                "count" : 100,
                "list"  : ["John", "小明"]
            }
        }`
    j, err := gjson.DecodeToJson([]byte(data))
    if err != nil {
        glog.Error(err)
    } else {
        c, _ := j.ToJson()
        fmt.Println("JSON:")
        fmt.Println(string(c))
        fmt.Println("======================")

        fmt.Println("XML:")
        c, _ = j.ToXmlIndent()
        fmt.Println(string(c))
        fmt.Println("======================")

        fmt.Println("YAML:")
        c, _ = j.ToYaml()
        fmt.Println(string(c))
        fmt.Println("======================")

        fmt.Println("TOML:")
        c, _ = j.ToToml()
        fmt.Println(string(c))
    }
    ```
    gjson支持将JSON转换为其他的数据格式，目前支持：JSON、XML、YAML/YML、TOML数据格式之间的相互转换。
    
    
    
    
    